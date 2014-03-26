---
layout: post
title: ruby 是怎么实现 prime 的
tags: [ruby prime]
---

## 写在前面

在 [Project Euler][] 上做题的过程中，时不时会碰见和质数相关的题目。
到现在，我记得已经碰见过诸如：
1. 判断一个数是否是质数，
2. 第XX个质数，
3. 前XX个数中的所有质数，
4. 质因数分解，
这样的问题不止一次。

每次遇到，都变着法儿的去实现，到最后，实在不想写了，直接用 *Ruby* 自带的 *prime*。

最近，遇到了 [Euler's Totient function][], 和质因数分解有些关系。

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


## Gererator23

质因数分解默认的生成器是 `Prime::Generator23`，下面是其具体代码。

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

> PS：我也是使用这种方法实现素数判断的，代码远没有如此精简。

但，一个数不被2和3整除不代表它就是素数，`Generator23` 只是一个伪素数生成器。


## PseudoPrimeGenerator

它的父类 `PseudoPrimeGenerator` 代码如下：

```ruby
# An abstract class for enumerating pseudo-prime numbers.
#
# Concrete subclasses should override succ, next, rewind.
class PseudoPrimeGenerator
  include Enumerable

  def initialize(ubound = nil)
    @ubound = ubound
  end

  def upper_bound=(ubound)
    @ubound = ubound
  end
  def upper_bound
    @ubound
  end

  # returns the next pseudo-prime number, and move the internal
  # position forward.
  #
  # +PseudoPrimeGenerator+#succ raises +NotImplementedError+.
  def succ
    raise NotImplementedError, "need to define `succ'"
  end

  # alias of +succ+.
  def next
    raise NotImplementedError, "need to define `next'"
  end

  # Rewinds the internal position for enumeration.
  #
  # See +Enumerator+#rewind.
  def rewind
    raise NotImplementedError, "need to define `rewind'"
  end

  # Iterates the given block for each prime number.
  def each(&block)
    return self.dup unless block
    if @ubound
      last_value = nil
      loop do
        prime = succ
        break last_value if prime > @ubound
        last_value = block.call(prime)
      end
    else
      loop do
        block.call(succ)
      end
    end
  end

  # see +Enumerator+#with_index.
  alias with_index each_with_index

  # see +Enumerator+#with_object.
  def with_object(obj)
    return enum_for(:with_object) unless block_given?
    each do |prime|
      yield prime, obj
    end
  end
end
```

`PseudoPrimeGenerator` 包含 `Enumerable`，实现了 `each` 方法；同时，作为父类，又提供
`succ`、`next`、`rewind` 给子类去实现。

除了 `Generator23`，`PseudoPrimeGenerator` 还有两个子类，

- EratosthenesGenerator
- TrialDivisionGenerator


## EratosthenesGenerator

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

`compute_primes`是这个生成器的核心。该实现在基本的 Sieve of Eratosthenes 算法上进行了两点改进：

- 偶数直接筛掉。
- 只计算到不大于 `sqrt(n).floor` 的那个质数。

`(-(segment_min + 1 + sieving_primes[i]) / 2) % sieving_primes[i]` 这行代码可以说是画龙点睛之笔。不过，代码可以更加函数化，比如说：

```ruby
segment.each do |prime|
  @primes.push prime unless prime.nil?
end

# ===>

@primes.concat segment.reject {|p| p.nil? }
```


## TrialDivisionGenerator

```ruby
  # An implementation of +PseudoPrimeGenerator+ which uses
  # a prime table generated by trial division.
  class TrialDivisionGenerator<PseudoPrimeGenerator
    def initialize
      @index = -1
      super
    end

    def succ
      TrialDivision.instance[@index += 1]
    end
    def rewind
      initialize
    end
    alias next succ
  end
  # Internal use. An implementation of prime table by trial division method.
  class TrialDivision
    include Singleton

    def initialize # :nodoc:
      # These are included as class variables to cache them for later uses.  If memory
      #   usage is a problem, they can be put in Prime#initialize as instance variables.

      # There must be no primes between @primes[-1] and @next_to_check.
      @primes = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101]
      # @next_to_check % 6 must be 1.
      @next_to_check = 103            # @primes[-1] - @primes[-1] % 6 + 7
      @ulticheck_index = 3            # @primes.index(@primes.reverse.find {|n|
      #   n < Math.sqrt(@@next_to_check) })
      @ulticheck_next_squared = 121   # @primes[@ulticheck_index + 1] ** 2
    end

    # Returns the cached prime numbers.
    def cache
      return @primes
    end
    alias primes cache
    alias primes_so_far cache

    # Returns the +index+th prime number.
    #
    # +index+ is a 0-based index.
    def [](index)
      while index >= @primes.length
        # Only check for prime factors up to the square root of the potential primes,
        #   but without the performance hit of an actual square root calculation.
        if @next_to_check + 4 > @ulticheck_next_squared
          @ulticheck_index += 1
          @ulticheck_next_squared = @primes.at(@ulticheck_index + 1) ** 2
        end
        # Only check numbers congruent to one and five, modulo six. All others

        #   are divisible by two or three.  This also allows us to skip checking against
        #   two and three.
        @primes.push @next_to_check if @primes[2..@ulticheck_index].find {|prime| @next_to_check % prime == 0 }.nil?
        @next_to_check += 4
        @primes.push @next_to_check if @primes[2..@ulticheck_index].find {|prime| @next_to_check % prime == 0 }.nil?
        @next_to_check += 2
      end
      return @primes[index]
    end
  end
```

## 最后

展示下源代码所使用的元编程：

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

因为`method_add`方法，才有 `Prime.each` 这样的调用。



[project euler]: http://projecteuler.net
[Euler's Totient function]: http://mathworld.wolfram.com/TotientFunction.html
[试除法]: http://en.wikipedia.org/wiki/Trial_division
