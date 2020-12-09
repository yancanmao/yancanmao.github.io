---

layout:     post
title:      "Flink Window operator"
subtitle:   ""
date:       2020-12-08
author:     "MYC"
header-img: "img/post-bg-universe.jpg"
catalog: true
tags:
    - streaming
---


## Window

Main components of window operator:

 - Window assigner: create new window as soon as the first element that should belong to this window arrives.
 - Trigger: specifies the conditions under which the window is considered ready for the function to be applied.
 - Function: contain the computation to be applied to the contents of the window (ProcessWindowFunction, ReduceFunction, AggregateFunction or FoldFunction)
 - Evictor: remove elements from the window after the trigger fires and before and/or after the function is applied.

### Tumbling Windows

A *tumbling windows* assigner assigns each element to a window of a specified *window size*. Tumbling windows have a fixed size and do not overlap. 



![img](https://ci.apache.org/projects/flink/flink-docs-release-1.11/fig/tumbling-windows.svg)

### Sliding Windows

The *sliding windows* assigner assigns elements to windows of fixed length. Sliding windows can be overlapping if the slide is smaller than the window size. 

![img](https://ci.apache.org/projects/flink/flink-docs-release-1.11/fig/sliding-windows.svg)

### Session Windows

The *session windows* assigner groups elements by sessions of activity. Session windows do not overlap and do not have a fixed start and end time, in contrast to *tumbling windows* and *sliding windows*. 

![img](https://ci.apache.org/projects/flink/flink-docs-release-1.11/fig/session-windows.svg)

### Global Windows

A *global windows* assigner assigns all elements with the same key to the same single *global window*. This windowing scheme is only useful if you also specify a custom [trigger](https://ci.apache.org/projects/flink/flink-docs-stable/dev/stream/operators/windows.html#triggers). 

![img](https://ci.apache.org/projects/flink/flink-docs-release-1.11/fig/non-windowed.svg)

## Window Functions

 It is used to process the elements of each (possibly keyed) window once the system determines that a window is ready for processing.

The window function can be one of `ReduceFunction`, `AggregateFunction`, `FoldFunction` or `ProcessWindowFunction`. 

The first two can be executed more efficiently (see [State Size](https://ci.apache.org/projects/flink/flink-docs-stable/dev/stream/operators/windows.html#state size) section) because Flink can incrementally aggregate the elements for each window as they arrive.

A `ProcessWindowFunction` gets an `Iterable` for all the elements contained in a window and additional meta information about the window to which the elements belong. A windowed transformation with a `ProcessWindowFunction` cannot be executed as efficiently as the other cases because Flink has to buffer *all* elements for a window internally before invoking the function.

### ReduceFunction

A `ReduceFunction` specifies how two elements from the input are combined to produce an output element of the same type. Flink uses a `ReduceFunction` to incrementally aggregate the elements of a window.

```java
DataStream<Tuple2<String, Long>> input = ...;

input
    .keyBy(<key selector>)
    .window(<window assigner>)
    .reduce(new ReduceFunction<Tuple2<String, Long>> {
      public Tuple2<String, Long> reduce(Tuple2<String, Long> v1, Tuple2<String, Long> v2) {
        return new Tuple2<>(v1.f0, v1.f1 + v2.f1);
      }
    });
```

### AggregateFunction

An `AggregateFunction` is a generalized version of a `ReduceFunction` that has three types: an input type (`IN`), accumulator type (`ACC`), and an output type (`OUT`). 

The input type is the type of elements in the input stream and the `AggregateFunction` has a method for adding one input element to an accumulator. 

```java
/**
 * The accumulator is used to keep a running sum and a count. The {@code getResult} method
 * computes the average.
 */
private static class AverageAggregate
    implements AggregateFunction<Tuple2<String, Long>, Tuple2<Long, Long>, Double> {
  @Override
  public Tuple2<Long, Long> createAccumulator() {
    return new Tuple2<>(0L, 0L);
  }

  @Override
  public Tuple2<Long, Long> add(Tuple2<String, Long> value, Tuple2<Long, Long> accumulator) {
    return new Tuple2<>(accumulator.f0 + value.f1, accumulator.f1 + 1L);
  }

  @Override
  public Double getResult(Tuple2<Long, Long> accumulator) {
    return ((double) accumulator.f0) / accumulator.f1;
  }

  @Override
  public Tuple2<Long, Long> merge(Tuple2<Long, Long> a, Tuple2<Long, Long> b) {
    return new Tuple2<>(a.f0 + b.f0, a.f1 + b.f1);
  }
}

DataStream<Tuple2<String, Long>> input = ...;

input
    .keyBy(<key selector>)
    .window(<window assigner>)
    .aggregate(new AverageAggregate());
```

### FoldFunction

A `FoldFunction` specifies how an input element of the window is combined with an element of the output type. 

```java
DataStream<Tuple2<String, Long>> input = ...;

input
    .keyBy(<key selector>)
    .window(<window assigner>)
    .fold("", new FoldFunction<Tuple2<String, Long>, String>> {
       public String fold(String acc, Tuple2<String, Long> value) {
         return acc + value.f1;
       }
    });
```

### ProcessWindowFunction

A ProcessWindowFunction gets an Iterable containing all the elements of the window, and a Context object with access to time and state information, which enables it to provide more flexibility than other window functions.

```java
public abstract class ProcessWindowFunction<IN, OUT, KEY, W extends Window> implements Function {

    /**
     * Evaluates the window and outputs none or several elements.
     *
     * @param key The key for which this window is evaluated.
     * @param context The context in which the window is being evaluated.
     * @param elements The elements in the window being evaluated.
     * @param out A collector for emitting elements.
     *
     * @throws Exception The function may throw exceptions to fail the program and trigger recovery.
     */
    public abstract void process(
            KEY key,
            Context context,
            Iterable<IN> elements,
            Collector<OUT> out) throws Exception;

   	/**
   	 * The context holding window metadata.
   	 */
   	public abstract class Context implements java.io.Serializable {
   	    /**
   	     * Returns the window that is being evaluated.
   	     */
   	    public abstract W window();

   	    /** Returns the current processing time. */
   	    public abstract long currentProcessingTime();

   	    /** Returns the current event-time watermark. */
   	    public abstract long currentWatermark();

   	    /**
   	     * State accessor for per-key and per-window state.
   	     *
   	     * <p><b>NOTE:</b>If you use per-window state you have to ensure that you clean it up
   	     * by implementing {@link ProcessWindowFunction#clear(Context)}.
   	     */
   	    public abstract KeyedStateStore windowState();

   	    /**
   	     * State accessor for per-key global state.
   	     */
   	    public abstract KeyedStateStore globalState();
   	}

}
```

### Using per-window state in ProcessWindowFunction

In addition to accessing keyed state (as any rich function can) a `ProcessWindowFunction` can also use keyed state that is scoped to the window that the function is currently processing. 

In this context it is important to understand what the window that *per-window* state is referring to is. There are different “windows” involved:

Per-window state is tied to the latter of those two. Meaning that if we process events for 1000 different keys and events for all of them currently fall into the *[12:00, 13:00)* time window then there will be 1000 window instances that each have their own keyed per-window state.