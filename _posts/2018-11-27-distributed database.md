## Distributed Database

#### Distributed Database design: Fragmentation

Data fragmentation: Horizontal fragmentation, Vertical fragmentation, Hybrid fragmentation

#### Distributed Query Processing

Join Strategies: Collocated Join(0), Directed Join(size(R)), Broadcast Join((n − 1) × size(R)), Repartitioned Join(size(R) + size(S))

#### Storage & Indexing

LSM = Log-Structured Merge: Targeted at write-intensive workloads

Compaction Strategies: Size-tiered Compaction Strategy (STCS)

Leveled Compaction Strategy (LCS): F = compaction factor, Compact when $Size(L) > F^L$

Optimizing SSTable Search: Sparse Index(find block in table), Bloom Filter(find key in block)

Indexing: Local Indexing, Global Indexing

#### Distributed Commit Protocols

ACID properties: Atomicity, Consistency, Isolation, Durability

Xact failures, System failures, Media failures

Transaction originating site ,Transaction coordinator (TC), Transaction Manager (TM)

Commit protocol: 2PC, 3PC (Before sends messages, writes log records, reflect its state.)

2PC: prepare(coordinator see global result), commit. (block)

3PC: prepare, precommit(inform others global result), commit. (non-block)

Recovery protocol: restarted servers do things.

Termination protocol: timer runs out will invoke termination protocol(either abort or resend its msg)

Cooperative Termination (CT) Protocol: coordinator fail, one P ask all other Ps for final state.

New Coordinator Termination Protocol: a new coordinator, State-request message.

#### Distributed Concurrency Control

view equivalent, view serializable schedule

conflict equivalent, conflict serializable schedule $\to$ view serializable schedule

Lock-Based Concurrency Control:

Two Phase Locking (2PL) Protocol (conflict serializable)

Strict Two Phase Locking (S2PL) (conflict serializable & recoverable)

Multiversion Concurrency Control: not blocked

multiversion view equivalent (S $\equiv_{mv}$ S′): have the same set of read-from relationships

monoversion schedule: read most recently created version

serial monoversion schedule: if it is a serial schedule

multiversion view serializable schedule (MVSS): exists serial monoversion schedule

view serializable schedule (VSS) $\to$ multiversion view serializable schedule
(MVSS)

Snapshot isolation:

Concurrent Update Property: First Committer Wins (FCW) Rule, First Updater Wins (FUW) Rule

Serializable Snapshot Isolation (SSI) Protocol: Detects non-serializable schedules

Concurrency Control Protocols: 

Distributed Lock-based Protocols: Centralized 2PL (C2PL), Distributed 2PL (D2PL)
Centralized Snapshot Isolation (CSI): Centralized Coordinator (CC)

#### Data Replication

One-copy database = non-replicated database

one-copy serializable (1SR): the same effect as executes on a one-copy database

mutually consistency: all the replicas have the identical values, strong consis, weak consis.

Replicated data (RD) schedules, One-copy (1C) schedules

Statement-based replication: send updates to replicas

Replication Protocols: Eager(Read-One-Write-All (ROWA) protocols), Lazy, Centralized, Distributed

Lazy Distributed Protocol: Reconciliation of Inconsistent Updates: Last-Writer-Wins heuristic

#### Replicated Data Consistency

Consistent prefix and monotonic reads

![1543232060813](C:\Users\Yancan\AppData\Roaming\Typora\typora-user-images\1543232060813.png)

#### Distributed Query Optimization

$Communication \ cost = T_{MSG}× (number \ of \ messages) + T_{TR}× (size \ of \ transferred \ data)$

Selectivity Factors

$SF_{SJ}(S.A) $= semijoin selectivity factor of S.A

$SF_{SJ}(S.A)  = \frac{card(πA(S))}{|domain(A)|} $

SDD-1 Algorithm:

1. For each relation, apply local reductions (i.e., selections, projections)

1. Iteratively select the most beneficial semijoin until none is applicable
2. Determine assembly site to compute joins
3. Post-optimization: eliminate unnecessary semijoin reductions

$card(R′) = card(R) × SF_{SJ} (S.A)$

$size(R′) = card(R′) × length(R)$

Full Reducer, Ear Reduction

semijoin|cost|benefit|benefit-cost

relation X| size(X)	   attribute X.A|SF_sj(X.A)|size(\piA(X))