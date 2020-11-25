---
layout:     post
title:      "Internet Architecture"
subtitle:   ""
date:       2018-12-03
author:     "MYC"
header-img: "img/post-bg-universe.jpg"
catalog: true
tags:
    - 学习 
    - 技术
---


## Internet Architecture

#### performance

**Network Performance Metrics**

Link Rate/Bandwidth/Capacity

Throughput(successful message delivery)

End-to-end delay

Response time

**Packet switching versus circuit switching:** 

- circuit-switching 10 users. reserved resources
- packet switching: 35 users. on-demand allocation

**Little’s Law: $L = \lambda W$**

Arrival rate: 𝜆

\# of customers/packets in the system: 𝐿

Sojourn time 𝑊

#### Queueing

independent and identically distributed(i.i.d)

random variable (r.v.)

probability density function

exponential distribution: $F(x) = P\{T \le x\} = 1 - e^{- \lambda x}$ or $F\overline (x) = P\{T > x\} = e^{- \lambda x}$

Memoryless property: $P\{T>s+t|T > s\} = P\{T>t\}$

**M/M/1 Model**

percentage of time that the “server” is busy, probability a random observation finds server busy

Utilization: $\rho = \frac{\lambda}{\mu}$

Percentage of time that exactly $i$ packets or customer in the system, i.e., packets or customers in the system, i.e., server + queue.

MM1 system: $\pi_{i} = P]\{L=i\}=\rho^i(1-\rho)$

Average \# of packet in the system: $E(L) = \frac{\rho}{1-\rho}$

Average sojourn time of packets: $E(W) = \frac{1}{\mu - \lambda}$

Average \# of packets in the queue $E(Q) = \frac{\rho^2}{1-\rho}$

Avg. queueing delay of packets $E(D) = E(W) - \frac{1}{\mu}$

the joint probability: $P\{L_1= j, L_2 = k\} = P\{L_1 = j\}P\{L_2 = k\} = \rho_1^j(1-\rho)\rho_2^k(1-\rho_2)$

**Jackson Network**: $\lambda_i = r_i+\sum_{j=1}^{n} \lambda_j P_{ji}$ 

#### Resource Allocation

reason: Internet provides best-effort service

Solution: allocate different amount resource to different application flows.

Fairness: Equal share of resources 

**Max-Min Fairness**

Max-Min Fair: distributed mechanisms are needed.

Generalized Process Sharing (GPS)

Weighted Fair Queueing (WFQ): packet-based and work-conserving 

Virtual Departure Time

virtual finish time

#### Software Defined Networking

network management 

networks are still notoriously hard to manage

Data Plane: processing and delivery of packets 

Control Plane: establishing the state in routers

Benefits of Separation:

Independent evolution and development 

Control from high-level software program

Benefits of Centralization:

Centralized decisions are easier to make 

Logically vs. physically centralized: SDN logically centralized

modularize the network control problem. 

network virtualization: SDN case study

#### BGP

AS: Autonomous System

Link state algorithm: all routers have complete topology, link cost info, Open Shortest Path First (OSPF)

Distance vector algorithm: router knows neighbors, link costs , Routing Information Protocol (RIP)

Distance Vector (DV) approach

Path-Vector(PV) Routing

Border Gateway Protocol (BGP): the de facto inter-domain routing protocol 