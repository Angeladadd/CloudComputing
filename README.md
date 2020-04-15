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


