---
layout: post
title: Ruby 的 prime gem
tags: [ruby prime]
---


如果你在 [Project Euler][] 上做过题，你一定碰见过和质数相关的题目。
到现在，我已经碰见过诸如：
1. 判断一个数是否是质数，
2. 第XX个质数，
3. 前XX个数中的所有质数，
4. 质因数分解，
这样的问题不止一次。


最近，遇到了 “[Euler's Totient function][]”, 和质因数分解有些关系，
心血来潮看了下 Ruby prime gem 的代码。

## 质因数分解

下面是质因数分解的代码：

```ruby
def prime_division(value, generator = Prime::Generator23.new)
  raise ZeroDivisionError if value == 0
  if value < 0
    value = -value
    pv = [[-1, 1]]
  else
    pv = []
  end
  for prime in generator
    count = 0
    while (value1, mod = value.divmod(prime)
           mod) == 0
      value = value1
      count += 1
    end
    if count != 0
      pv.push [prime, count]
    end
    break if value1 <= prime
  end
  if value > 1
    pv.push [value, 1]
  end
  return pv
end
```

代码异常简练，使用的是最简单直接的[试除法][]，并借助了质数生成器。


## 生成器 - Gererator23

质因数分解默认的生成器是 `Prime::Generator23`：

```ruby
# Generates all integers which are greater than 2 and
# are not divisible by either 2 or 3.
#
# This is a pseudo-prime generator, suitable on
# checking primality of an integer by brute force
# method.
class Generator23<PseudoPrimeGenerator
  def initialize
    @prime = 1
    @step = nil
    super
  end

  def succ
    loop do
      if (@step)
        @prime += @step
        @step = 6 - @step
      else
        case @prime
        when 1; @prime = 2
        when 2; @prime = 3
        when 3; @prime = 5; @step = 2
        end
      end
      return @prime
    end
  end
  alias next succ
  def rewind
    initialize
  end
end
```

正如名字所暗含的一样，`Generator23` 生成了所有不被2和3整除的数，代码的实现非常灵巧。

> 我也是使用这种方法实现素数判断的，但代码远远不如它精简。好的代码可以提高 Programmer 的觉悟。

但，一个数不被 2 和 3 整除不代表它就是素数，`Generator23` 只是一个伪素数生成器。


它的父类是 `PseudoPrimeGenerator`，
`PseudoPrimeGenerator` 包含 `Enumerable`，实现了 `each` 方法；
同时，作为父类，又提供 `succ`、`next`、`rewind` 抽象方法
（Ruby 中没有抽象方法的概念，这里只是借助其含义）。

除了 `Generator23`，`PseudoPrimeGenerator` 还有两个子类，

- EratosthenesGenerator
- TrialDivisionGenerator


## 生成器 - EratosthenesGenerator

```ruby
  # An implementation of +PseudoPrimeGenerator+.
  #
  # Uses +EratosthenesSieve+.
  class EratosthenesGenerator < PseudoPrimeGenerator
    def initialize
      @last_prime_index = -1
      super
    end

    def succ
      @last_prime_index += 1
      EratosthenesSieve.instance.get_nth_prime(@last_prime_index)
    end
    def rewind
      initialize
    end
    alias next succ
  end
  # Internal use. An implementation of eratosthenes' sieve
  class EratosthenesSieve
    include Singleton

    def initialize
      @primes = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101]
      # @max_checked must be an even number
      @max_checked = @primes.last + 1
    end

    def get_nth_prime(n)
      compute_primes while @primes.size <= n
      @primes[n]
    end

    private
    def compute_primes
      # max_segment_size must be an even number
      max_segment_size = 1e6.to_i
      max_cached_prime = @primes.last
      # do not double count primes if #compute_primes is interrupted
      # by Timeout.timeout
      @max_checked = max_cached_prime + 1 if max_cached_prime > @max_checked

      segment_min = @max_checked
      segment_max = [segment_min + max_segment_size, max_cached_prime * 2].min
      root = Integer(Math.sqrt(segment_max).floor)

      sieving_primes = @primes[1 .. -1].take_while { |prime| prime <= root }
      offsets = Array.new(sieving_primes.size) do |i|
        (-(segment_min + 1 + sieving_primes[i]) / 2) % sieving_primes[i]
      end

      segment = ((segment_min + 1) .. segment_max).step(2).to_a
      sieving_primes.each_with_index do |prime, index|
        composite_index = offsets[index]
        while composite_index < segment.size do
          segment[composite_index] = nil
          composite_index += prime
        end
      end

      segment.each do |prime|
        @primes.push prime unless prime.nil?
      end
      @max_checked = segment_max
    end
  end

```

`compute_primes` 是这个生成器的核心。
该实现在基本的 Sieve of Eratosthenes 算法上做了两点改进：

- 偶数直接筛掉。
- 只计算到不大于 `sqrt(n).floor` 的那个质数。

`(-(segment_min + 1 + sieving_primes[i]) / 2) % sieving_primes[i]` 这行代码是画龙点睛之笔。不过，代码可以更加函数化，比如说：

```ruby
segment.each do |prime|
  @primes.push prime unless prime.nil?
end

# ===>

@primes.concat segment.reject {|p| p.nil? }
```


## Prime 中的元编程

Ruby 中，元编程无孔不入：

```ruby
class Prime
  include Enumerable
  @the_instance = Prime.new

  #...

  class << self
    extend Forwardable
    include Enumerable
    # Returns the default instance of Prime.
    def instance; @the_instance end

    def method_added(method)
      (class<< self;self;end).def_delegator :instance, method
    end
  end
end
```

因为 `method_add` 方法，才会 `Prime.each` 这样的调用。



[project euler]: http://projecteuler.net
[Euler's Totient function]: http://mathworld.wolfram.com/TotientFunction.html
[试除法]: http://en.wikipedia.org/wiki/Trial_division
