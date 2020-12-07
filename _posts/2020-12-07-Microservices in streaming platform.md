---

layout:     post
title:      "Microervices in streaming"
subtitle:   ""
date:       2020-12-07
author:     "MYC"
header-img: "img/post-bg-universe.jpg"
catalog: true
tags:
    - streaming
---


## What is Microservices?

Microservices are Built, Deployed and Managed Independently

Microservices are an architectural approach to software development based on building an application as a collection of small services. There is no standard definition for what amount of code constitutes a “small service.” But some experts say it’s about the size at which a team can query the service’s health with a single request that returns a “yes” or “no” answer. Similarly, a service is too big if it requires more than one team to manage it.

Each service has its own unique and well-defined role, runs in its own process and communicates via HTTP APIs or messaging. 

Each microservice can be deployed, upgraded, scaled and restarted independently of all the sibling services in the application. 

They are typically orchestrated by an automated system, making it possible to have frequent updates of live applications without affecting the end users.

### Pros and Cons of Mircoservices

Microservices are an evolution of a service-oriented architecture (SOA). 
Loosely-coupled services (aka microservices) are now feasible to implement and manage at scale.

By enabling small autonomous teams to develop, deploy and scale their respective services independently, microservices essentially parallelize development, thereby speeding up the production cycle exponentially.

Container-based — and open source — systems like Docker and Kubernetes are a very effective way to develop, deploy and manage microservices.
Many mature and robust tools, platforms and other services already exist in the container space, rendering containerization a natural fit for microservices-based applications.


With a microservices architecture, service discovery, networking, testing and monitoring all become more complex and difficult to manage following reliable older systems and practices.

These services are not aware of each other until you step in to orchestrate them. 

Despite the abundant potential benefits, microservices are not the right solution for every project. A well-built monolithic architecture can scale just as well, and remains the best option, in many scenarios.

## Microervices in streaming

Some of these architectures, such as inter-process communications (IPC) and shared memory, continue to work perfectly well for applications that are able to run on a single server. 
And others, such as the service-oriented architecture (SOA) and representational state transfer (REST), work well enough for many applications that need to be distributed across multiple servers.

But for the growing number of applications being implemented as orchestrated pipelines of containerized microservices, a more capable and versatile inter-service communications technology is needed. 

This new technology must meet two objectives: 
Higher throughput of transactions.
Easy to achieve many benefits. (easy to use?)

Among the advantages of a well-executed microservices architecture is greater agility and scalability, a simplified development and testing environment, less disruptive integration of new and enhanced capabilities, and more granular service monitoring and troubleshooting.

Containers have emerged as the ideal technology for running microservices. By minimizing or eliminating overhead, containers make efficient use of available compute and storage resources, enabling them to deliver peak performance and scalability for all microservices needed in any application. 

The problem is: Microservices running in containers have a much greater need for inter-service communications than traditional architectures do.

These two basic approaches have recently been combined to create a distributed streaming platform that supports multiple publishers and subscribers in a way that is secure and scalable—and simple to use with a containerized microservices architecture.

Distributed streaming platform should have 3 capabilities:

1. Publish and subscribe to streams of messages in a way that is similar to how a message queue or messaging system operates.
2. Store or persist streams of messages in a fault-tolerant manner.
3. Process streams in real-time as they occur.

Another way to characterize these capabilities is the three P’s that make streaming platforms Pervasive, Persistent and Performant.

A major advantage of publish/subscribe streaming platforms is the “decoupled” nature of all communications.

This decoupling eliminates the need for publishers to track — or even be aware of — any subscribers, and makes it possible for any and all subscribers to have access to any published streams. 

The result is the ability to add new publishers and subscribers without any risk of disruption to any existing microservices.

Application-level pipelines are created by simply chaining together multiple microservices, each of which subscribes to any data stream it needs to perform its designated service and, optionally, publishes its own stream for use by other microservices. 

### References

[Why Microservices Running in Containers Need a Streaming Platform](https://thenewstack.io/microservices-running-containers-need-streaming-platform/)