.. _theory:

分布式理论知识
=========================================

本章介绍后端常用到的分布式理论知识，包括 cap，base理论，一致性协议算法等。

一致性模型
----------------------------------------

弱一致性(最终一致性):

- DNS(Domain Name System)
- Gossip


强一致性:

- 主从同步(一致性高，可用性低)

  - master 接受写清球
  - master 复制日志到 slave
  - master 等待直到所有从库返回。

- Paxos
- Raft (multi-paxos)
- ZAB (multi-paxos)

Paxos
-----------------------------------------

Bacic Paxos
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

角色：

- Client: 系统外部角色，请求发起者。(民众)
- Proposer: 接收 client 请求，向集群踢出提议(propose)。(议员)
- Accpetor(Voter): 提议投票和接收者，只有在形成法定人数(Quorum，一般为majority多数派)时，提议才会被接受。(过会)
- Learner: 提议接受者, backup，备份。对一群一致性无影响。(记录员)

阶段：

- 1a: Prepare: Proposer提出一个提案编号N，此 N 大于这个 proposer 之前踢出的编号。请求 Accpetor的quorum接
- 1b: Promise: 请求 N 大于此 Acceptor 之前接受的任何提案编号则接受，否则拒绝
- 2a: Accept: 如果达到了多数派,Proposer 会发出 accept 请求(包含N)和提案内容
- 2b: Accepted: 如果Acceptor在此期间没有接收到任何编号大于N的提案，则接受此提案内容，否则忽略

潜在问题：活锁(liveness)或dueling。解决方案，随机超时

难实现，效率低（2轮RPC)，活锁

Multi Paxos
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

新概念，Leader： 唯一的Proposer，所有请求都需要经过此 Leader

Fast Paxos
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Raft
-----------------------------------------
