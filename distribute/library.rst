.. _library:

=========================================
微服务分布式系统组件
=========================================

介绍一些后端系统组件

限流
----------------------

某些业务需要针对 IP 或者用户来进行一个限流操作，减少恶意请求或者减轻服务器压力。
限流可以在很多层面去做，比如 nginx+lua, API Gateway，或者在接口层直接来做，一般可以结合 redis 来做。
限流有一下一些实现方式，可以根据业务场景选择：

- token bucket。令牌桶算法
- redis incr/expire。最简单的一种方式，通过 redis 针对用户或者 ip key 来计数
- redis zset。可以实现基于时间窗口来限流。不过不适合短期内大量 qps 限流，适合用户行为限流

断路器(Circuit Breaker)
-------------------------------------------

在分布式系统中，为了防止级联错误，导致服务血崩，经常需要使用断路器来保护系统。断路器原理如下：

.. image:: ../_image/distribute/熔断器原理.png

Netflix 开源的 Hystrix 是比较流行的开源实现，对应的有各种其他语言的实现，比如 Hystrix-go

参考:

- `Circuit Breaker and Retry  <https://medium.com/@trongdan_tran/circuit-breaker-and-retry-64830e71d0f6>`_


服务发现
----------------------
