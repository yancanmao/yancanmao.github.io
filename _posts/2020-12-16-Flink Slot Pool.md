---

layout:     post
title:      "Flink Slot Pool"
subtitle:   ""
date:       2020-12-08
author:     "MYC"
header-img: "img/post-bg-universe.jpg"
catalog: true
tags:
    - streaming
---


## Flink Slot Pool

Slot is the minimum resource allocation unit in Flink, every slot may contain a certain number of resources such as CPU and Memory. 
Every slot can be shared by a group of tasks, the tasks should not belong to the instance from the same operator.
In this way, Flink acheives higher resource utilization and reduced the number of slots will be used for each streaming jobs. 
For example, Source and Sink tasks can be placed co-located with their downstream/upstream tasks to reduce the slot usage.
One of concern is: I am not sure how could Flink achieve CPU isolation and allocation, this I would imagine should be complicated and difficult.

Example of a query with one a FlatMap operator:

```
Allocated Slot: 121ba782cb52352da2a72ae8f6cff67e
- Sink (1/1)
- Splitter FlatMap (3/4)

Allocated Slot: fe477d84310242a9a9280c67ec21db16
- Splitter FlatMap (1/4)

Allocated Slot: 4840aade9d04d3a36a0a9b2201ea2fb1
- Source: Custom Source (1/1)
- Splitter FlatMap (4/4)

Allocated Slot: dc48df41d427702e72b09060d716acc9
- Splitter FlatMap (2/4)
```

In this example, Source and Sink have been placed with FlatMap in an allocated slot.

In Flink, there are two abstractions, one is `LogicalSlot` another is `PhysicalSlot`, every task belongs to a `LogicalSlot`, and multiple `LogicalSlot` can be mapped to a single `PhysicalSlot` by forming a tree structure.

### allocateSlot

There is a SlotSharingGroup pre-defined during the JobGraph construction, and all tasks from the same job will be allocated to the same SlotSharingGroup. This part is hard-code i.e. all tasks are belonging to the same Group, you cannot release the resource unless all slot from the group is released.

```java
// Execution allocateSlot:
public CompletableFuture<Execution> allocateAndAssignSlotForExecution(){
    slotProvider.allocateSlot(
        slotRequestId,
        toSchedule,
        new SlotProfile(
            ResourceProfile.UNKNOWN,
            preferredLocations,
            previousAllocationIDs,
            allPreviousExecutionGraphAllocationIds),
        queued,
        allocationTimeout));
}

public CompletableFuture<LogicalSlot> allocateSlot(
		SlotRequestId slotRequestId,
		ScheduledUnit scheduledUnit,
		SlotProfile slotProfile,
		boolean allowQueuedScheduling,
		Time allocationTimeout) {
    scheduledUnit.getSlotSharingGroupId() == null ?
        allocateSingleSlot() : allocateSharedSlot()
}

private CompletableFuture<LogicalSlot> allocateSingleSlot(){
    tryAllocateFromAvailable(slotRequestId, slotProfile);
    if (slotAndLocality.isPresent()) {
        completeAllocationByAssigningPayload();
    } else {
        slotPool.requestNewAllocatedSlot();
        completeAllocationByAssigningPayload();
    }
}

private CompletableFuture<LogicalSlot> allocateSharedSlot(){
    allocateMultiTaskSlot()
}

private SlotSharingManager.MultiTaskSlotLocality allocateMultiTaskSlot(){
    tryAllocateFromAvailable(allocatedSlotRequestId, slotProfile);
}

public CompletableFuture<PhysicalSlot> requestNewAllocatedSlot(
		@Nonnull SlotRequestId slotRequestId,
		@Nonnull ResourceProfile resourceProfile,
		Time timeout) {
    return requestNewAllocatedSlotInternal(slotRequestId, resourceProfile, timeout)
			.thenApply((Function.identity()));
}

private CompletableFuture<AllocatedSlot> requestNewAllocatedSlotInternal(
		@Nonnull SlotRequestId slotRequestId,
		@Nonnull ResourceProfile resourceProfile,
		@Nonnull Time timeout) {
    if (resourceManagerGateway == null) {
        stashRequestWaitingForResourceManager(pendingRequest);
    } else {
        requestSlotFromResourceManager(resourceManagerGateway, pendingRequest);
    }
}

private void requestSlotFromResourceManager(
			final ResourceManagerGateway resourceManagerGateway,
			final PendingRequest pendingRequest) {
    resourceManagerGateway.requestSlot();
}
```

### releaseSlot

When canceling tasks, there is a SlotPoolManager to check whether the slot is used by a group, and if the group of tasks has all reached the terminate state.  This is one of the confusing part in the SlotPool. Note that every SlotPool is owned by a ExecutionGraph in JobManager, where this is owned by a Streaming job rather than the Cluster itself. It aquire new slots from the ResourceManager when it cannot serve a slot request.

When the task state has reached either CANCELED or FINISHED, the task will try to release the slot it have. Because the allocated slot is always a `LogicalSlot`, it need to check whether the `PhysicalSlot` can be released.

We show source code of how this procedure works in Flink-1.8.1:

When terminated state is detected e.g. FINISHED, the task will be marked as Finished and invoke the release slot.

```java
// ExecutionGraph check state update request from TaskManager
public boolean updateState(TaskExecutionState state) {
    ...
	case FINISHED:
        // this deserialization is exception-free
        accumulators = deserializeAccumulators(state);
        attempt.markFinished(accumulators, state.getIOMetrics());
        return true;

    case CANCELED:
    // this deserialization is exception-free
    accumulators = deserializeAccumulators(state);
    attempt.completeCancelling(accumulators, state.getIOMetrics());
    return true;
	...
}

// In Execution, it will invoke this method.
void markFinished(Map<String, Accumulator<?, ?>> userAccumulators, IOMetrics metrics) {
    ...
    releaseAssignedResource(null);
    ...
}

// The execution will find the corresponding assigned resources, and release the logical slot.
private void releaseAssignedResource(@Nullable Throwable cause) {
    ...
    final LogicalSlot slot = assignedResource;
    slot.releaseSlot(cause)
    ...
}

// if not considering shared slot, there is only a SingleLogicalSlot will be allocated.
// thus in SingleLogicalSlot, it will invoke the releaseSlot
public CompletableFuture<?> releaseSlot(@Nullable Throwable cause) {
    ...
   	returnSlotToOwner(payload.getTerminalStateFuture());
    ...
}

// this slot will be finally released by calling the slotOwner for returning logic.
private void returnSlotToOwner(CompletableFuture<?> terminalStateFuture) {
    slotOwner.returnLogicalSlot(this);
}

// SlotOwner is an Interface, this is implemented by a SchedulerImpl who is managing the actual resources.
// Each LogicalSlot has a requestId, and also has a slotSharingGroupId.
// In SchedulerImpl
public void returnLogicalSlot(LogicalSlot logicalSlot) {
    cancelSlotRequest(slotRequestId, slotSharingGroupId, cause);
}

// This is the most important part for releasing the LogicalSlot, we can see the logic is that if the slot do not have a slot id, the slotPool will try to release the slot, the slot is the allocated slot i.e. physical slot. However, if the slot belongs to a shared group, then only the logical slot will be removed, the physical slot will be resverved for other LogicalSlot.
public void cancelSlotRequest(
		SlotRequestId slotRequestId,
		@Nullable SlotSharingGroupId slotSharingGroupId,
		Throwable cause) {
    if (slotSharingGroupId != null) {
        releaseSharedSlot(slotRequestId, slotSharingGroupId, cause);
    } else {
        slotPool.releaseSlot(slotRequestId, cause);
    }
}

private void releaseSharedSlot(
		@Nonnull SlotRequestId slotRequestId,
		@Nonnull SlotSharingGroupId slotSharingGroupId,
		Throwable cause) {
    final TaskSlot taskSlot = multiTaskSlotManager.getTaskSlot(slotRequestId);
    taskSlot.release(cause);
}

public void releaseSlot() {
    releaseSingleSlot(slotRequestId, cause);
}

private void releaseSingleSlot(SlotRequestId slotRequestId, Throwable cause) {
    allocatedSlot.releasePayload(cause);
    tryFulfillSlotRequestOrMakeAvailable(allocatedSlot);
}
```