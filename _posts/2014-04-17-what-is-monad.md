---
layout: post
title: Monad 是什么
tags: [functional-programming, monad]
---


## Monad 是什么

### 定义

一个`monad`包含三个东西：

1. 一个类型构造器`M`，它可以从类型`a`构造出类型`M[a]`。
2. 一个`unit`函数，将类型为`a`的值转换成类型`M[a]`，函数签名为： `a => M[a]`。
3. 一个`bind`函数，通过函数`a => M[b]`将一个`M[a]`转换成一个`M[b]`，函数签名：`(M[a], a => M[b]) => M[b]`。概念类似于`flatMap`。

### 另一种构造方式

`flatMap`通常可以用`map`和`flatten`代替。
类似的概念，Monad中的`bind`函数可以用`fmap`和`join`定义。（`fmap`相当于`map`，`join`相当于`flatten`）

- `fmap`，通过函数`a => b`将一个`M[a]`转换成`M[b]`，函数签名：`(M[a], a => b) => M[b]`。
- `join`，将一个`M[M[a]]`转换成`M[a]`，函数签名：`M[M[a]] => M[a]`。

等价的概念：`bind(m, f) = join(fmap(m, f))`。


### Mother of Monads - Continuation Monad

```scala
case class M[+A](in: (A => Any) => Any);

def unit[A](x: A) = M { k: (A => Any) => k(x) }

def bind[A, B](m: M[A], f: A => M[B]): M[B] =
  M { k: (B => Any) =>
    m.in { x: A =>
      f(x).in(k)
    }
  }
```
