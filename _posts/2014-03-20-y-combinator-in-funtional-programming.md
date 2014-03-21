---
layout: post
title: Y combinator是个什么玩意儿
tags: [y-combinator funtional-programming todo]
---

> 今天想给自己挖个坑，这个坑就是[Y Combinator](http://en.wikipedia.org/wiki/Fixed-point_combinator#Y_combinator)。（PG开了一家创投公司，名字也叫Y Combinator）

我对Y Combinator的兴趣，源自于王垠的一篇讲丘奇和图灵的文章（目前，王垠的博客已关闭，现在已经看不到了，以后很可能都看不到了），还有刘未鹏的[康托尔、哥德尔、图灵，永恒的金色对角线](http://mindhacks.cn/2006/10/15/cantor-godel-turing-an-eternal-golden-diagonal/)。

网络上，还有其他很多关于Y Combinator的东西，不过感觉都大同小异。个人推荐：

- [great explanation from Mike Vanier](http://mvanier.livejournal.com/2897.html)，写的十分通俗易懂。

- PLLC这本书也有详细的推导过程。（想要深入理解Y Combinator或者函数式编程，这本书应该是不二之选）


### 更新

#### Y Combinator for lazy language:

``` scheme
(define Y
  (lambda (f)
    ((lambda (x) (f (x x)))
     (lambda (x) (f (x x))))))
; for a given function f (which is a non-recursive function like almost-factorial),
; the corresponding recursive function can be obtained
; first by computing (lambda (x) (f (x x))),
; and then applying this lambda expression to itself.
; This is the usual definition of the normal-order Y combinator.
```

验证 (Y f) = (f (Y f))。

```
(Y f)
= ((lambda (x) (f (x x)))
   (lambda (x) (f (x x))))))
= (f ((lambda (x) (f (x x)))
      (lambda (x) (f (x x)))))
= (f (Y f))
```

#### Y Combinator fro strict language:

```scheme
; just replace (x x) with (lambda (y) ((x x) y))
(define Y
  (lambda (f)
    ((lambda (x) (x x))
     (lambda (x) (f (lambda (y) ((x x) y)))))))
```
