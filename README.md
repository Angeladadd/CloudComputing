# introduction to cloud

### 云的定义
cloud=lots of storage + compute cycles (计算周期)nearby

* single site云 数据中心
    * 计算节点
    * 交换机
    * 网络拓扑
    * 存储（后端）节点
    * 前端接受客户请求
    * 软件服务
* geo distributed cloud
    * 多个节点
    * 每个节点可能有不同的软件服务

### 按需访问 aaS

* HaaS: hardware as a service
* IaaS: infrastructure as a service 虚拟化VM
* PaaS: platform write code..不用管VM
* SaaS: software eg. google doc

### 分布式系统

# MapReduce

* 从函数式编程而来
* map: process each record sequentially and independently
* reduce: process set of all records in batches

* map: parallelly, process individual record, key value pairs
* reduce: merge, parrallelly processes and merges by partitioning keys

### examples

#### distributed grep
* map: emit line
* reduce: output

#### reverse web-link graph
 * map : \<source, target\> to \<target, source\>
 * reduce : \<target, list\>

#### count of url access frequency
* map: \<url, 1\>
* reduce: \< url, count\>
* map: \<1, (url, count)\> 为什么 key 是 1: 因为要把所有的加起来，Batch应该是全部的。
* reduce: sum up

#### sort
* map: \<key,value\> to \<value, whatever\>
* reduce: 
* partition function: 一个范围的values
* map的输出会用快排排序，reduce的输出会用merge sort排序

### MapReduce scheduling

#### programming
* user
    * write map & reduce program
    * submit and wait
* internal
    * parallelize map: send to servers
    * transform data from map to reduce: partitioning function
    * parallelize reduce
    * storage of input and output: map input : distributed file systems, map output : local file system, reduce read remote disk write to distributed file system
    * barrier between map and reduce

####  Yarn scheduler
* resource manager: scheduling
* server as node:
    * node manager: server specific function & daemon( report container completed?
    * application master: container negotiation with RM and NMs, detecting task failures( ask for container

### MapReduce fault-tolerance
* server failure
   * NM heartbeats to RM
   * NM keeps track of each task running at its server
   * AM heartbeats to RM
* RM failure
   * piggyback

* Stragglers: speculative execution, 两个task同时运行，第一个完成了就标记为已完成
* Locality： 3 replicas

# Gossip(epidemic) protocol

### multicast
#### multicast vs broadcast
* broadcast: 消息送到整个网络上的所有节点
* multicast: 只送到一个特定的group

#### 要求
* 容错： ACK NAK机制
* 扩展性：树形

### gossip

* push gossip: sender周期性的随机选择b个target发送gossip message，用UDP传输，有了gossip message的结点周期性的选择b个target发送gossip message.....target可以重复被选，每一个节点有独立的时钟;对于多个gossip messages可以随机选择它的一个子集，或最近收到的，或最高优先级的
* pull gossip: send queries rather than messages, 被问的节点如果有这个message会返回一个copy

#### push gossip
* light weight
* fast
* highly fault tolerant

##### epidemiology
分析监狱、社会中传染病的传播

* n+1个混合的个体
* 每一对个体的接触概率$\beta$
* 每个时刻有y个感染者和x个未感染者
* initially x=n y=1 anytime x+y=n+1
* 感染者与未感染者接触则未感染者被感染

##### fault tolerance
* packet loss
    * 50%loss b to b/2 多跑两轮而已
* node failure
    * 50% nodes fail n to n/2 b to b/2
* 传言会很快消失吗？不会

#### pull gossip

* n/2的节点得到gossip: O(log N) in the best case
* 之后 pull 会比 push 更快，O(log log N)

#### hybird gossip
* push in the first round to get half nodes infected
* pull in the later rount to fasten the process of being infected

#### topology aware gossip

提高传递给同一个subnet的概率，若$subnet_i$有$n_i$个节点，那么这个subnet中的节点选择subnet外节点的概率设为$1/n_i$,这样跨subnet传输的复杂度将变为O(1)

