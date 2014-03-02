---
layout: post
title: summingbird 笔记
---

### Producer

构建处理逻辑的地方。

### Platform

Platform 定义：

``` scala
trait Platform[P <: Platform[P]] {
  type Source[_]
  type Store[_, _]
  type Sink[_]
  type Service[_, _]
  type Plan[_]

  def plan[T](completed: Producer[P, T]): Plan[T]
}
```

### Source

每个 `Platform` 都会有自己的数据源表示方式，比如：

  - Storm: `type Source[+T] = com.twitter.tormenta.spout.Spout[(Long, T)]`
  - Memory: `type Source[T] = TraversableOnce[T]`

### Store

a snapshot of the aggregated value of each key in `Store[K, V]`.

v, an instance of `Monoid[V]` ?

Summingbird uses `Monoid[V]`

### Sink

a stream, not a snapshot.

### Service

在 Memory 和 Storm 中，

`Service：Producer[P, (K, V)] leftJoin P#Service[K, RightV] => Prudocer[P, (K, (V, Option[RightV]))]`

### Plan

call `platform.plan(producer)` to generate a plan on a specific platform,
which is the last representation of MapReduce flow.
