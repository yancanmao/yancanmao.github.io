---
layout:     post
title:      "Distributed System"
subtitle:   ""
date:       2018-11-15
author:     "MYC"
header-img: "img/post-bg-universe.jpg"
catalog: true
tags:
    - 学习 
    - 技术
---

## Distributed System Skeleton

#### lecture2

OSI model

RPC goal: Simple. non-blocking RPC

MultiCast: Application-Level Multicast(p2p), IP Multicast

Gossip: O(logn) Gossip aggregation: sum, average, min... O(logn)

#### lecture3

Processes are expensive: creation cost, context switch, inter-process communication(all of them will involve OS) -- same app no need isolation.

Threads are cheap: three aspect not via OS. user-level threads, kernel-level threads, LWP. block problem(non-blocking, upcall).

Thread pool: used in Dist sys, pre request a pool of threads, dispatch threads for incoming jobs, optimize thread creation cost, more stable against perk load.

Event driven Architecture: problem: single Thread maintain all CPU state information of each request. 

Event driven: highly specialized thread package, every request has its own thread package, no need to maintain other requests' CPU state, context switch will faster.

stateful-stateless: try to be stateless.

Server Cluster: tier architecture.

#### lecture4

Name System: Merge name spaces: new root node, mounting.

Simple Naming: resolve a name: broad cast, forwarding pointers, home-based approach.

DNS.

#### lecture5

Physical Clock sync motivation: Clock rate drift, clock drift

Physical clock sync solve problem: clock drift

Cristian's protocol assumption: same request and reply delay.

Network Time protocol: sync with multiple servers, pick smallest d. delay minimum. server need UTC

The Berkeley alg: no accurate time all servers.

Software clock: no external clocks. infer ordering of events.

Software clock assumption: atomic is important.

logical clock: clock 1 < 5 !=> event 1 happens before 5.

vector clock: clock 1 < 5 => event 1 happens before 5.

mutual exclusion: guarantee atomic? lock manager election

Token based approach: use a leader server

Voting: quorum vote to get lock

Leader Election: 

#### lecture6

**Consistency model:**

- Sequential consistency: all processors execute in the same order, sequence may not the same with global time order.

- linearizability consistency: sequential consistency and time follow global time.

- Eventual consistency: all replica will eventually be the same, so as we see, final state should be the same.

- Continuous Consistency: one model named TACT, eventual consistency always be ensured

- - final image of continuous consistency: numerical error, order error, temporal error.

**Consistency protocol:**

linearizability/sequential consistency protocol: 

- primary-based protocol: all request handled by primary, backup just replicate and read. read once write all.

- - app uses primary-based protocol: Software Distributed Shared Memory

- - failure dealing of primary-based protocol: lease of replica, backup kill itself if no leases.

- Replicated State Machine Protocol: read/write majority. instruction of each node should be the same.

Eventual Consistency protocol: 

- The Anti-entropy protocol: write/read all, instructions execute in every node, apply cmd in the same order is important.

- - keep cmds the same order: logical clock, happen-before, Committed and Tentative Write.

- - how to find committed and tentative write: use a sequencer to identify. (A,2,1)

Continuous Consistency protocol:(only detection, and alg I assume it is black box)

- Temporal Error TACT: real-time vector clock, compute TE according to curtime - vetor x_i

- Numerical Error TACT: has a global NE stats server to compute the total NE.

- Order Error TACT: number of Tentative write is Order Error, detect and use normal alg to solve it.

Replica Replacement: NP hard, global view of info.

#### lecture7

Nodes Failure model

- Crash Failure
- Byzantine Failure

Communication channels failure: no failure or unreliable(drop msg)

Timing model

- Sync model: msg delay is bounded, node speed guarantee, accurate failure detection.
- Async model: msg delay is not bounded, node speed is not guarantee.

Distributed Consensus: failure-free: vote, xact commit, write ordering.

under failure: Node Crash/Byzantine, channels unreliable, sync/async.

termination, agreement

#### lecture8

p2p: 

unstructured file sharing: central directory server, super-peers, flooding flat p2p topology, random walks flat p2p topology.

structured file sharing: DHT