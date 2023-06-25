# Distributed System Notes

## Lesson 1: What are distributed systems?

![pic](https://static001.geekbang.org/resource/image/8c/a4/8c0e6e7100efdfbf57412840a3fddda4.jpg)

Two types of distributed systems: **OLTP** (online transaction processing) and **OLAP** (online analytical processing).

- **RPO**: recovery point objective; the maximum amount of data loss that is acceptable

- **RTO**: recovery time objective; the maximum amount of time that is acceptable for the system to be down

### Characteristics of OLTP systems

- **Write More Read Less**: less complicated queries; no need for complex aggregations

- **Low Latency**: usually within 500ms; unacceptable to have a query that takes 5 seconds to run

- **High Throughput**: with no limitation with the expansion of the business

### Inside Archetecture

1. **Client-side Components + Monolithic Database** (客户端组件 + 单体数据库)

>通过独立的逻辑层建立数据分片和路由规则，实现单体数据库的初步管理，使应用能够对接多个单体数据库，实现并发、存储能力的扩展。其作为应用系统的一部分，对业务侵入比较深。

Example: Sharding-JDBC

![pic](https://static001.geekbang.org/resource/image/7a/da/7a86887e1f8f97f8a660c9434febc9da.jpg?wh=2700*1341)

2. **Proxy Middleware + Monolithic Database** (代理中间件 + 单体数据库)

> 以独立中间件的方式，管理数据规则和路由规则，以独立进程存在，与业务应用层和单体数据库相隔离，减少了对应用的影响。随着代理中间件的发展，还会衍生出部分分布式事务处理能力。

Example: MyCat

![pic](https://static001.geekbang.org/resource/image/88/ec/88728291d4c48a8a999bd56a04488cec.jpg?wh=2700*1437)

3. **Unitized Architecture + Monolithic Database** (单体化架构 + 单体数据库)

> 单元化架构是对业务应用系统的彻底重构，应用系统被拆分成若干实例，配置独立的单体数据库，让每个实例管理一定范围的数据。例如对于银行贷款系统，可以为每个支行搭建独立的应用实例，管理支行各自的用户，当出现跨支行业务时，由应用层代码通过分布式事务组件保证事务的 ACID 特性。

![pic](https://static001.geekbang.org/resource/image/4b/97/4b41ffef868c2277ae40580cd2044997.jpg?wh=2700*1401)

### Definition of distributed systems from the OLTP perspective 

#### From the Outside Characteristics

> 分布式数据库是服务于写多读少、低延时、海量并发 OLTP 场景的，具备海量数据存储能力和高可靠性的关系型数据库

Distributed databases are **relational databases** that serve scenarios with **more writes than reads**, **low latency**, and **massive concurrent** OLTP (Online Transaction Processing). They possess the capability to **store massive amounts of data** and have **high reliability**.

## Lesson 2: Strong Consistency

![pic](https://static001.geekbang.org/resource/image/27/af/27155b05c028b261yyc2d3c3469a3faf.jpg?wh=2700*2008)

### CAP Theorem

Any distributed data store can provide only two of the following three guarantees:

- **Consistency**: all nodes see the same data at the same time

- **Availability**: a guarantee that every request receives a response about whether it was successful or failed

- **Partition Tolerance**: the system continues to operate despite arbitrary message loss or failure of part of the system

<img src="https://upload.wikimedia.org/wikipedia/commons/c/c6/CAP_Theorem_Venn_Diagram.png" height="400">

### ACID

- **Atomicity**: each statement in a transaction (to read, write, update or delete data) is treated as a single unit.

- **Consistency**: ensures that transactions only make changes to tables in predefined, predictable ways.

- **Isolation**: when multiple users are reading and writing from the same table all at once, isolation of their transactions ensures that the concurrent transactions don't interfere with or affect one another.

- **Durability**: ensures that changes to your data made by successfully executed transactions will be saved, even in the event of system failure.

### Data Consistency: State Consistency

> State Consistency: the data is consistent at any given time



> Operation Consistency: the consistency of data that external users can read through agreed-upon protocol operations


