---
layout: post
title: 对 self-type annotation 最好的解释
---

Martin Odersky 在 stackoverflow 上的一个回答足胜过千言万语：

![Martin Odersky 在 stackoverflow 上对 self-type annotation 的解释]({{site.url}}/assets/2014/06/11/explanation-to-self-type-annoation-of-martin-odersky.png)


总结来说，self-type annotation 有两个作用：

1. 使用 `this: A =>` 可以限定继承关系：一个类只有作为 `B` 时才能继承或混入 `A` 。
2. 使用 `outer =>` 为 `this` 定义别名 `outer` ：内部有子类型定义，需要引用外部类。

所以可以有下面这三种用法：

``` scala
trait A

// 1. 限定继承关系
trait B { this: A =>
 // do somthing here
}

// 2. 设定别名
trait C { outer =>
  trait D {
    // reference outer
  }
}

// 3. 既限定关系，又设定别名
trait D { outer: A =>
  // do something and reference outer
}
```
