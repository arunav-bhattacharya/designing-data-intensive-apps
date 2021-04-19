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

Most applications are built by layering one data model on top of another. For each layer, the key question is: how is it represented in terms of the next-lower layer? For example:
1. As an application developer, you look at the real world (in which there are peo‐ ple, organizations, goods, actions, money flows, sensors, etc.) and model it in terms of objects or data structures, and APIs that manipulate those data struc‐ tures. Those structures are often specific to your application.
2. When you want to store those data structures, you express them in terms of a general-purpose data model, such as JSON or XML documents, tables in a rela‐ tional database, or a graph model.
3. The engineers who built your database software decided on a way of representing that JSON/XML/relational/graph data in terms of bytes in memory, on disk, or on a network. The representation may allow the data to be queried, searched, manipulated, and processed in various ways.
4. On yet lower levels, hardware engineers have figured out how to represent bytes in terms of electrical currents, pulses of light, magnetic fields, and more.

#### Relational vs Document Model

- The roots of relational databases lie in *business data processing*, which was performed on mainframe computers in the 1960s and ’70s. The use cases appear mundane from today’s perspective: typically *transaction processing* (entering sales or banking transactions, airline reservations, stock-keeping in warehouses) and *batch processing* (customer invoicing, payroll, reporting).
- In the 2010s, **NoSQL** is the latest attempt to overthrow the relational model’s dominance. It has been retroactively reinterpreted as ***Not Only SQL***. There are several driving forces behind the adoption of NoSQL databases:
  -  A need for greater scalability than relational databases 
  -  A widespread preference for free and open source software 
  -  Specialized query operations that are not well supported by the relational model
  -  Frustration with the restrictiveness of relational schemas and desire for more dynamic and expressive data model

#### Object Relational Mismatch

- Most application development today is done in object-oriented programming languages, which leads to a common criticism of the SQL data model: if data is stored in relational tables, an awkward translation layer is required between the objects in the application code and the database model of tables, rows, and columns. The disconnect between the models is sometimes called an impedance mismatch.
- Object-relational mapping (ORM) frameworks like ActiveRecord and Hibernate reduce the amount of boilerplate code required for this translation layer, but they can’t completely hide the differences between the two models.

#### One-to-Many Relationship

- Consider the below image. In the traditional SQL model, the most common normalized representation is to put `positions`, `education`, and `contact_info` in separate tables, with a foreign key reference to the `users` table
- Later versions of the SQL standard added support for structured datatypes and XML data; this allowed multi-valued data to be stored within a single row, with support for querying and indexing inside those documents. These features are supported to varying degrees by Oracle, IBM DB2, MS SQL Server, and PostgreSQL. A JSON datatype is also supported by several databases, including IBM DB2, MySQL, and PostgreSQL.
- A third option is to encode jobs, education, and contact info as a JSON or XML document, store it on a text column in the database, and let the application interpret its structure and content. In this setup, you typically cannot use the database to query for values inside that encoded column.

![linkedin profile](/images/linkedin.png "linkedin-profile")

- For a data structure like a résumé, which is mostly a self-contained document, a JSON representation can be quite appropriate. JSON has the appeal of being much simpler than XML. Document-oriented databases like MongoDB, RethinkDB, CouchDB, Espresso, etc support this data model.

```json
{
  "user_id": 251,
  "first_name": "Bill",
  "last_name": "Gates",
  "summary": "Co-chair of the Bill & Melinda Gates... Active blogger.", "region_id": "us:91",
  "industry_id": 131,
  "photo_url": "/p/7/000/253/05b/308dd6e.jpg",
  "positions": [
    {"job_title": "Co-chair", "organization": "Bill & Melinda Gates Foundation"}, 
    {"job_title": "Co-founder, Chairman", "organization": "Microsoft"}
  ], 
  "education": [
    {"school_name": "Harvard University", "start": 1973, "end": 1975},
    {"school_name": "Lakeside School, Seattle", "start": null, "end": null} 
  ],
  "contact_info": {
  "blog": "http://thegatesnotes.com", "twitter": "http://twitter.com/BillGates"
  } 
}
```

- The JSON representation has better locality than the multi-table schema. If you want to fetch a profile in the relational example, you need to either perform multiple queries (query each table by `user_id`) or perform a messy multiway join between the users table and its subordinate tables. In the JSON representation, all the relevant information is in one place, and one query is sufficient.
- The one-to-many relationships from the user profile to the user’s `positions`, `education` history, and `contact_info` imply a tree structure in the data, and the JSON representation makes this tree structure explicit.

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