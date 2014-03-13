---
layout: post
title: 函数accumulate
tags: [functional-programming]
---

今天看[SICP][1]时，遇到`accumulate`的实现。
（Ruby中的`inject`，Scala中的`fold`表达的都是类似功能）

``` racket
(define (accumulate op initial sequence)
  (if (null? sequence)
      initial
      (op (car sequence)
          (accumulate op initial (cdr sequence)))))
```

当时想，这个可以转换 **iterative process** 吧，于是改写成了下面这样的代码。

``` racket
(define (accumulate op initial sequence)
  (if (null? sequence)
      initial
      (accumulate op (op (car sequence) initial) (cdr sequence))))
```

完后，还自我得意了一会儿。
不过在做习题时，卡在了一个地方，测试的时候，才察觉出自己的实现和原文实现的差别。

原文的实现，是从右到左的，而我的则相反。比如说，对于`(accumulate - 0 '(5 4 3 2 1))`。

从右到左是这样的：

``` racket
(- 5 (- 4 (- 3 (- 2 (- 1 0))))) ; => (5-(4-(3-(2-(1-0)))))
```

而从左到右则大不相同：

``` racket
(- (- (- (- (- 0 5) 4) 3) 2) 1) ; => (0-5-4-3-2-1)
```

这也是为什么，从左到右时，`initial`一般是`op`的第一个参数，而从右到左时，却是第二个。

想到这里的时候，脑子里突然冒出了[stackoverflow上的一个问答][2]，
当时在了解scala时遇到的问题，今天突然就有了所以然。

不得不赞一下SICP这本书。

[1]: http://mitpress.mit.edu/sicp/full-text/book/book.html "计算机程序的构造与解释"
[2]: http://stackoverflow.com/questions/17408880/reduce-fold-or-scan-left-right "reduce, fold or scan(left/right)?"
