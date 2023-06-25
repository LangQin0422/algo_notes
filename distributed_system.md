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

#### Strong Consistency

Example: Fully Synchronous Replication (全同步复制) in MySQL

![pic](https://static001.geekbang.org/resource/image/eb/1d/eb572abd30b3f77cb001c339ba37851d.jpg)

* Cons:

    - Low Performance: The main database must wait until all the replicas have written the data before returning a response to the client.
  
    - Reliabiliy: The probability of failure increases exponentially with the number of replicas.

#### Weak Consistency

1. **Read after write consistency** (Read My Writes Consistency)

> 写后读一致性

![pic](https://static001.geekbang.org/resource/image/7d/dc/7df116a9b10b1d9ce0b5a8400c00eedc.jpg)

> 要特别注意的是，小明有一个再次刷新朋友圈的动作，这时如果访问副本 R2，由于其尚未完成同步，情侣照将会消失，小明就会觉得自己的照片被弄丢了。此处，我们假定系统可以通过某种策略由写入节点的主副本 R1 负责后续的读取操作，这样就实现了写后读一致性，可以保证小明再次读取到照片。

2. **Monotonic Read Consistency**

> 单调读一致性

Exampe:
> 在小明发布照片后的瞬间，小红也刷新了朋友圈，此时读取到副本 R1，所以小红看到了照片；片刻之后，小红再次刷新，此时读取到的副本是 R2，于是照片消失了。小红以为小明删除了照片，但实际上这完全是程序错误造成的，数据向后回滚，出现了“时光倒流”。

![pic](https://static001.geekbang.org/resource/image/b1/a8/b138fa2ebe6c6cc60bd7dcbf4fa06da8.jpg)

> 实现单调读一致性的方式，可以是将用户与副本建立固定的映射关系，比如使用哈希算法将用户 ID 映射到固定副本上，这样避免了在多个副本中切换，也就不会出现上面的异常了。
> 
One way to achieve monotonic read consistency is to establish a fixed mapping relationship between the user and the replica, such as using a hash algorithm to map the user ID to a fixed replica. This avoids switching between multiple replicas and therefore prevents the above-mentioned anomaly.

3. **Consistent Prefix**

> 前缀一致性

Example:
> 这天小明去看 CBA 总决赛，刚开球小明就拍了一张现场照片发到朋友圈，想要炫耀一下。小红也很喜欢篮球，但临时有事没有去现场，就在评论区问小明：“现在比分是多少？”小明回复：“4:2。”小明的同学，远在加拿大的小刚，却看到了一个奇怪的现象，评论区先出现了小明的回复“4:2。”，而后才刷到小红的评论“现在比分是多少？”。难道小明能够预知未来吗？

![pic](https://static001.geekbang.org/resource/image/26/23/2635951c5260b0b97de5fed08f368a23.jpg)

> 显然，问题与答案之间是有因果关系的，但这种关系在复制的过程中被忽略了，于是出现了异常。

Constent prefix is the consistency that maintains such a causal relationship.

4. **Linearizability**

> 线性一致性
>
> 在“前缀一致性”的案例中，问题与答案之间存在一种显式声明，但在现实中，多数场景的因果关系更加复杂，也不可能要求全部做显式声明。
>
> 线性一致性（Linearizability）就是建立在事件的先后顺序之上的。在线性一致性下，整个系统表现得好像只有一个副本，所有操作被记录在一条时间线上，并且被原子化，这样任意两个事件都可以比较先后顺序。

Linearizability is built upon the sequence of events. Under linearizability, the whole system appears as if there is only one replica, all operations are recorded on a single timeline, and are atomized, so that the sequence of any two events can be compared. Such order is determined by the introduction of Global clock (全局时钟).
  
5. **Causal Consistency**
   
> 因果一致性
>
> 因果一致性的基础是偏序关系，也就是说，部分事件顺序是可以比较的。至少一个节点内部的事件是可以排序的，依靠节点的本地时钟就行了；节点间如果发生通讯，则参与通讯的两个事件也是可以排序的，接收方的事件一定晚于调用方的事件。

The basis of causal consistency is partial ordering, that is, the order of some events can be **compared**. At least the events within one node can be ordered, relying on the node's local clock; if communication occurs between nodes, the two events involved in the communication can also be ordered, the receiver's event is always later than the caller's event.

* Logic Clocks

> 借助逻辑时钟仍然可以建立全序关系，当然这个全序关系是不够精确的。因为如果两个事件并不相关，那么逻辑时钟给出的大小关系是没有意义的。


> Operation Consistency: the consistency of data that external users can read through agreed-upon protocol operations


