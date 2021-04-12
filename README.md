# Designing Data Intensive Applications

This document is essentially a summary of the book - "Designing Data Intensive Applications" by Martin Kleppmann. As I was reading through the book I jotted the important relevant points from the book in this document.

<br>

## Part I - Foundation of Data Systems

### 1. Reliable, Scalable and Maintainable Applications

A data-intensive application is typically built from standard building blocks that provide commonly needed functionality. For example, many applications need to:
- Store data so that they, or another application, can find it again later (*databases*)
- Remember the result of an expensive operation, to speed up reads (*caches*)
- Allow users to search data by keyword or filter it in various ways (*search indexes*)
- Send a message to another process, to be handled asynchronously (*stream processing*)
- Periodically crunch a large amount of accumulated data (*batch processing*)

This book focusses primarily on 3 aspects -
- Reliability
- Scalability
- Maintainability

#### Reliability 

Reliability means making systems work correctly, even when faults occur. Faults can be in hardware (typically random and uncorrelated), software (bugs are typically systematic and hard to deal with), and humans (who inevitably make mistakes from time to time). Fault-tolerance techniques can hide certain types of faults from the end user.

Types of faults -
  - Hardware Faults
  - Software Errors
  - Human errors

#### Scalability

Scalability means having strategies for keeping performance good, even when load increases. In order to discuss scalability, we first need ways of describing load and performance quantitatively. We looked at Twitter’s home timelines as an example of describing load, and response time percentiles as a way of measuring performance. In a scalable system, you can add processing capacity in order to remain reliable under high load.

- **Describe Load**
  
  Load can be described with a few numbers which we call load parameters. The best choice of parameters depends on the architecture of your system: it may be requests per second to a web server, the ratio of reads to writes in a database, the number of simultaneously active users in a chat room, the hit rate on a cache, or something else. 

- **Describe Performance**
  
  When load increases there might be impacts to performance:
  - When you increase a load parameter and keep the system resources (CPU, memory, network bandwidth, etc.) unchanged, how is the performance of your system affected?
  - When you increase a load parameter, how much do you need to increase the resources if you want to keep performance unchanged?

    > **Latency and Response Time**
    > - *Latency* and *response time* are often used synonymously, but they are not the same.
    > - The *response time* is what the client sees: besides the actual time to process the request (the service time), it includes network delays and queueing delays.
    > - *Latency* is the duration that a request is waiting to be handled — during which it is latent, awaiting service 
    > 
    > **Measuring Response Times**
    > - *Mean* is not a very good metric if you want to know your “typical” response time, because it doesn’t tell you how many users actually experienced that delay.
    > - It is better to use *percentiles*. If you take your list of response times and sort it from fastest to slowest, then the median is the halfway point: for example, if your median response time is 200 ms, that means half your requests return in less than 200 ms, and half your requests take longer than that.

- **Coping with Load**
  - People often talk of a dichotomy between ***scaling up*** (vertical scaling, moving to a more powerful machine) and ***scaling out*** (horizontal scaling, distributing the load across multiple smaller machines). In reality, good architectures usually involve a pragmatic mixture of approaches: for example, using several fairly powerful machines can still be simpler and cheaper than a large number of small virtual machines.
  - Some systems are ***elastic***, meaning that they can automatically add computing resources when they detect a load increase, whereas other systems are scaled manually (a human analyzes the capacity and decides to add more machines to the system). An elastic system can be useful if load is highly unpredictable, but manually scaled systems are simpler and may have fewer operational surprises.
  - Common wisdom until recently was to keep your database on a single node (scale up) until scaling cost or high-availability requirements forced us to make it distributed. As the tools and abstractions for distributed systems get better, this common wisdom may change, at least for some kinds of applications. It is conceivable that ***distributed data systems*** will become the default in the future, even for use cases that don’t handle large volumes of data or traffic.


The architecture of systems that operate at large scale is usually highly specific to the application—there is no such thing as a generic, one-size-fits-all scalable architecture (informally known as magic scaling sauce). The problem may be the volume of reads, the volume of writes, the volume of data to store, the complexity of the data, the response time requirements, the access patterns, or (usually) some mixture of all of these plus many more issues.

#### Maintainability

Maintainability has many facets, but in essence it’s about making life better for the engineering and operations teams who need to work with the system. Good *abstractions* can help reduce complexity and make the system easier to modify and adapt for new use cases. Good *operability* means having good visibility into the system’s health, and having effective ways of managing it.

##### Operability: Making Life Easy for Operations

Make it easy for operations teams to keep the system running smoothly. Good operations can often work around the limitations of bad (or incomplete) software, but good software cannot run reliably with bad operations.

##### Simplicity: Managing Complexity

Make it easy for new engineers to understand the system, by removing as much complexity as possible from the system. Moseley and Marks define complexity as *accidental* if it is not inherent in the problem that the software solves (as seen by the users) but arises only from the implementation.
One of the best tools we have for removing *accidental complexity* is abstraction. A good abstraction can hide a great deal of implementation detail behind a clean, simple-to-understand façade.

##### Evolvability: Making Change Easy

Make it easy for engineers to make changes to the system in the future, adapting it for unanticipated use cases as requirements change. Also known as extensibility, modifiability, or plasticity. In terms of organizational processes, *Agile* working patterns provide a framework for adapting to change. The Agile community has also developed technical tools and patterns that are helpful when developing software in a frequently changing environment, such as *test-driven development (TDD) and refactoring*.

### 2. Data Models and Query Languages

### 3. Storage and Retrieval

### 4. Encoding and Evolution

<br>

## Part II - Distributed Data

### 5. Replication

### 6. Partitioning

### 7. Transactions

### 8. Trouble with Distributed Systems

### 9. Consistency and Consensus

<br>

## Part III - Derived Data

### 10. Batch Processing

### 11. Stream Processing

### 12. Future of Data Systems