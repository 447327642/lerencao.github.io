---
layout: post
title: Ruby中的Lazy Evaluation
tags: ruby lazy-evaluation
---

> [Ruby 2.0.0: Enumerable::Lazy](http://magazine.rubyist.net/?Ruby200SpecialEn-lazy)

> [ruby-2-0-works-hard-so-you-can-be-lazy](http://patshaughnessy.net/2013/4/3/ruby-2-0-works-hard-so-you-can-be-lazy)

下面一段代码是 `Enumerable::Lazy` 实现的关键点：

``` ruby
do |yielder, value|
  result = yield value
  yielder.yield result
end
```
