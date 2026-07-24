---
layout: article
title: "System Design"
aside:
  toc: true
---

## General overview

## **General Tips**

- Scalable, reliable, cost optimal and performant systems
  - Ask questions about the scale, performance and whether it needs an API
  - Make assumptions and state them clearly

## Scaling

Scaling from zero to large number of users

1. Single server setup including web app, database, cache etc.
2. Separate web/mobile traffic (web tier) and database (data tier) servers to allow them to be scaled independently
3. Horizontal scaling
   1. Vertical scaling has a hard limit. Impossible to add unlimited memory and CPU onto a single server
   2. Vertical scaling doesn’t have failover and redundancy
4. Load balancer to evenly distribute incoming traffic among web servers that are defined in a load-balanced set
5. Database replication: This helps address failover and redundancy as well as scaling read operations
6. Cacheing: Temporary data store layer, faster than database can be used to improve load/response times.
7. Content Delivery Network: Geographically dispersed servers to deliver static content such as images, videos, CSS, JavaScript files etc. by caching

### Summary from system design interview by Alex Au

- Keep web tier stateless
- Build redundancy at every tier
- Cache data as much as you can
- Support multiple data centers
- Host static assets in CDN
- Scale your data tier by sharding
- Split tiers into individual services
- Monitor your system and use automation tools

## Database

### OLTP vs OLAP

- Online Transactions Processing
- Online Analytics Processing

|                     | OLTP                                                                                              | OLAP                                                                              |
| :------------------ | :------------------------------------------------------------------------------------------------ | :-------------------------------------------------------------------------------- |
| Characteristics     | Handles a large number of small transactions                                                      | Handles large volumes of data with complex queries                                |
| Query types         | Simple standardised queries                                                                       | Complex queries                                                                   |
| Operations          | Based on INSERT, UPDATE, DELETE commands                                                          | Based on SELECT commands to aggregate data for reporting                          |
| Response time       | Milliseconds                                                                                      | Seconds, minutes, or hours depending on the amount of data to process             |
| Design              | Industry-specific, such as retail, manufacturing, or banking                                      | Subject-specific, such as sales, inventory, or marketing                          |
| Source              | Transactions                                                                                      | Aggregated data from transactions                                                 |
| Purpose             | Control and run essential business operations in real time                                        | Plan, solve problems, support decisions, discover hidden insights                 |
| Data updates        | Short, fast updates initiated by user                                                             | Data periodically refreshed with scheduled, long-running batch jobs               |
| Space requirements  | Generally small if historical data is archived                                                    | Generally large due to aggregating large datasets                                 |
| Backup and recovery | Regular backups required to ensure business continuity and meet legal and governance requirements | Lost data can be reloaded from OLTP database as needed in lieu of regular backups |
| Productivity        | Increases productivity of end users                                                               | Increases productivity of business managers, data analysts, and executives        |
| Data view           | Lists day-to-day business transactions                                                            | Multi-dimensional view of enterprise data                                         |
| User examples       | Customer-facing personnel, clerks, online shoppers                                                | Knowledge workers such as data analysts, business analysts, and executives        |
| Database design     | Normalised databases for efficiency                                                               | Denormalised databases for analysis                                               |

### SQL vs NoSQL

| Aspect       | Relational       | NoSQL (Non Relational)                  |
| ------------ | ---------------- | --------------------------------------- |
| Data Storage | Rows and Columns | Key value, document, wide-column, graph |
| Schemas      | Fixed            | Dynamic                                 |
| Querying     | Using SQL        | Focussed on collection of documents     |
| Scalability  | Vertical         | Horizontal                              |
| Transactions | Supported        | Support varies                          |
| Consistency  | Strong           | Eventual and Strong                     |
|              |                  |                                         |

### Denormalisation vs Normalisation

|      | Denomalisation                                                                                                                              | Normalisation                                                                                                                                                    |
| :--- | :------------------------------------------------------------------------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pros | Faster data reads<br>Simpler queries advantageous to developers<br>Requires less compute on read operations<br>Makes data available quickly | Faster writes<br>No redundant data<br>Less database complexity<br>Data always consistent                                                                         |
| Cons | Slower writes<br>More database complexity<br>Potential for data inconsistency<br>Requires more storage,RAM                                  | Slower reads<br>Heavy querying can overwhelm, crash hardware<br>Table joins required since data isn't duplicated<br>Indexing not as efficient due to table joins |

### ACID

ACID refers to a standard set of properties that guarantee database transactions are processed. reliably. ACID considers how a database recovers from any failures that occur during the transaction. ACID-compliant DBMS ensures that data in the database remains accurate and consistent despite any such failures. All RDBMS's are ACID compliant and typically implement an OLTP system.

- Atomicity: Guarantee that either all of the transactions succeeds or none of it does. If one part of the transaction fails the whole transaction fails. It’s either “all or nothing”
- Consistency: Guarantee that the data will be consistent. All data will be valid according to all defined rules, including any constraints, cascades and triggers that have been applied on the database
- Isolation: Guarantees that all transactions will occur in isolation. No transaction will be affected by any other transaction. So a transaction cannot read data from any other transaction that has not yet completed.
- Durability: Once a transaction is committed, it will remain in the system even if there’s a system crash immediately following the transaction. Any changes from the transaction must be stored permanently. If the system tells the user that the transaction has succeeded, the transaction must have in fact succeeded.

[Read more about ACID databases](https://database.guide/what-is-acid-in-databases/)

**When to use an ACID DBMS?**

ACID provides the principles that database transactions should adhere to, to ensure that data doesn’t become corrupt as a result of a failure of some sort.

A transaction is a single logical operation that may consist of one or many steps. For example, transferring money between bank accounts (i.e. debiting one account and crediting the other) is a transaction.

If a transaction like this fails halfway through, it could have major consequences. Money could be debited from the first account but not credited to the other account.

This is where the ACID principles should apply.

According to the ACID definition, a database is consistent *if and only if* it contains the results of successful transactions. Any database that is ACID-compliant will ensure that only successful transactions are processed. If a failure occurs before a transaction completes, no data will be changed.

So ACID-compliant DBMSs provide organisations with the confidence that their database will maintain data integrity, even if some type of failure occurs while transactions are in the middle of being processed.

### Types of databases

- Key-value database: Used for storing key value pairs in a distributed manner (e.g. Amazon DynamoBB, Redis, or Cassandra)
- Document database: Used for storing JSON documents (e.g. MongoDB, Azure CosmosDB, CouchDB)
- Graph database: Used for storing data with complex relationships (e.g. Amazon Neptune, Neo4J or Tiger DB)
- Ledger database: Store using an append only record journal (e.g. Amazon QLDB)
- Distributed data processing system: Apache Hadoop/Spark to process tracking data
- Common stores

### CAP Theorem

CAP theorem states only 2 of the following 3 properties can be ensured in a distributed system.

- Consistency: All users see the same data as the same time
  - Consistency in CAP theorem doesn’t mean strongly consistent but rather slight eventual consistency with a very short unnoticeable lag time between nodes (as it’s not possible to have a strongly consistent distributed database due to network latency)
- Availability: System is always available for reads and writes (even with node failures)
- Partition Tolerance: System continues to function, even if communication fails between nodes

<aside>
⚠️ In system design we assume Network Partitions will happen so to offer any kind of reliable service partition tolerance is necessary. i.e can’t forfeit the P in CAP so tradeoff between Consistency and Availability
</aside>

Consistency vs Availability

- Prioritising consistency means rejecting write requests when any node is unavailable
- Prioritising availability means it’s acceptable to have inconsistent data across nodes where one node may contain stale data

## Useful Resources

- [System Design Roadmap by Karan Pratap Singh](https://github.com/karanpratapsingh/system-design): Excellent guide on scalability, queues, caching, and database design
- [System Design Primer by Donne Martin](https://github.com/donnemartin/system-design-primer): The industry-standard resource for learning distributed system design, caching strategies, load balancers, and CAP theorem trade-offs
