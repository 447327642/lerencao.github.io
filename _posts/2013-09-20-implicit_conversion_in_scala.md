---
layout: post
title: Scala 的隐式转换
tags: ["scala", "implicit conversion"]
---

### 什么是隐式转换（implicit conversion）

隐式转换是一个函数（take one argument, and return something），并用 `implicit` 标记。

### 什么时候执行隐式转换

1.  当传递参数给函数时，如果参数类型与预期的类型不同，Scala 会尝试使用隐式转换（寻找一个隐式转换，应用到参数上）。
2.  当在一个对象上调用函数时，如果找不到这个函数，Scala 会使用隐式转换，直到找到这个函数，或者失败。（示例见 *快学scala 21.2*）

###  隐式参数

函数和方法可以带有一个标记为 `implicit` 的参数列表。比如说：

``` scala
case class Delimiters(left: String, right: String)

def quote(what: String)(implicit delims: Delimiters) = delims.left + what + delims.right
```

1.  可以显式传递一个 `Delimiters` 对象给 `quote` 函数，像这样：

    ``` scala
    quote("this is a string")(Delimiters("<", ">"))
    ```

2.  也可以略去该参数，像这样：

    ``` scala
    quote("this is a string")
    ```

    这种情况，编译器会在如下两个地方，查找一个类型为 `Delimiters` 的 *隐式值*（必须被声明为 `implicit` 的值）
      - 当前作用域，所有可以用单个标识符指代的满足类型要求的 `val` 或 `def`
      - 与 **所要求类型相关联的类型** 的伴生对象
