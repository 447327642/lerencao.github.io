---
layout: post
title: 快学Scala习题解
tags: scala
published: false
---

## 第一章

1.

``` scala
%              &              *              +              -              /              >              >=             >>             >>>            ^              asInstanceOf   isInstanceOf
toByte         toChar         toDouble       toFloat        toInt          toLong         toShort        toString       unary_+        unary_-        unary_~        |
```

2.

``` scala
scala> 3 - math.pow(math.sqrt(3), 2)
res21: Double = 4.440892098500626E-16
```

3.

val

4.

``` scala
scala> "scala"*3
res22: String = scalascalascala
```

5.

10 和 2 比，谁更大。max方法定义在 RichInt 中

6.

``` scala
scala> val base: BigInt = 2
base: BigInt = 2
scala> base pow 1024
res29: scala.math.BigInt = 179769313486231590772930519078902473361797697894230657273430081157732675805500963132708477322407536021120113879871393357658789768814416622492847430639474124377767893424865485276302219601246094119453082952085005768838150682342462881473913110540827237163350510684586298239947245938479716304835356329624224137216
```

7.

``` scala
import BigInt.probablePrime
import util.Random
```

8.

``` scala
BigInt(100, util.Random).toString(36)
```

9.

``` scala
"scala".head
"scala".last
// where is not first ?
```

10.

``` scala
def drop(n: Int): String
// Selects all elements except first n ones.
def dropRight(n: Int): String
// Selects all elements except last n ones.
def drop(n: Int): String
// Selects all elements except first n ones.
def dropRight(n: Int): String
//Selects all elements except last n ones.
```
对于‘取前后连续的字符串’，以上操作更高效，但对于其他 substring 无能为力。

## 第二章

1.

``` scala
def signum(x: Double) = if (x > 0) 1 else if (x == 0) 0 else -1
```

2.

`(), Unit`

3.

当 x 类型为 Unit 的变量时，赋值语句 x = y = 1 合法

4.

``` scala
for(i <- 0 to 10; j = 10-i) println(j)
```

5.

``` scala
def countdown(n: Int) = {
    for(i <- 0 to n; j = n-i) println(j)
}
```

6.

``` scala
val aString = "Hello"
var res: Long = 1
for(c <- aString) res *= c.toInt
println(res)
```

7.

``` scala
"Hello".foldLeft(1.toLong) { (res, c) => res * c.toInt }
```

8.

``` scala
def product(s: String) = {
    s.foldLeft(BigInt(1)) { (res, c) => res * c.toInt }
}
```

9.

``` scala
def product(s: String): BigInt = {
    if(s.length == 0) 1
    else s.head.toInt * product(s.tail)
}
```

10.

``` scala
def pow(x: Double, n: Int): Double = {
    if(n > 0 && n%2 == 0) math.pow(pow(x, n/2), 2)
    else if(n > 0 && n%2 == 1) pow(x, n-1) * x
    else if(n == 0) 1
    else 1/pow(x, -n)
}
```

## 第三章

1.

``` scala
val a = new Array[Int](n)
for(i <- 0 until n) a(i) = until.Random.nextInt(n)
a
```

2.

``` scala
val a = Array(1,2,3,4,5,6,7,8)
for( i <- 0 until (a.length, 2)) {
    if(i < a.length-1) {
        val tmp = a(i)
        a(i) = a(i+1)
        a(i+1) = tmp
    }
}
a
```

3.

``` scala
val a = Array(1,2,3,4,5,6,7,8)
val b = for( i <- 0 until (a.length)) yield {
  if (i%2 == 0) { if (i == a.length -1) a(i) else a(i+1) }
  else a(i-1)
}
a
b
```

4.

``` scala
val b = a.filter(_ > 0) ++ b.filter(_ <= 0)
```

5.

``` scala
val a = Array(1.0,1.1,1.2,1.3,1.4,1.5)
val average = a.sum/a.length
```

6.

`a.sorted.reverse`

7.

`a.distinct`

8.

``` scala
import scala.collection.mutable.ArrayBuffer
val a = ArrayBuffer(1,-2,3,-4,5,-6,7)
val negative_indexes = for (i <- 0 until a.length if a(i)<0) yield i
val to_be_droped = negative_indexes.reverse.dropRight(1)
for( i <- to_be_droped) {
  a.remove(i)
}
println(a.mkString(","))
```

9.

`java.util.TimeZone.getAvailableIDs.filter(_.startsWith("America")).map(_.drop(8)).sorted`

10.

``` scala
import java.awt.datatransfer._
import scala.collection.mutable.Buffer
val flavors = SystemFlavorMap.getDefaultFlavorMap().asInstanceOf[SystemFlavorMap]
val fvr: Buffer[String] = flavors.getNativesForFlavor(DataFlavor.imageFlavor)
```

## 第四章

1.

``` scala
val a = Map("a"->10, "b"->30)
val b = for( (k,v) <- a) yield (k, v*0.9)
```

2.

``` scala
val in = new java.util.Scanner(new java.io.File("my-file.txt"))
val wcs = new scala.collection.mutable.HashMap[String, Int]
while(in.hasNext()){
    val key = in.next()
    wcs(key) = if(wcs.contains(key)) wcs(key) + 1 else 1
}
println(wcs)
```
这个解法可以更 scala 一些~

3.

``` scala
val in = new java.util.Scanner(new java.io.File("my-file.txt"))
var wcs = Map[String, Int]()
while(in.hasNext()){
  val key = in.next()
  val value = if (wcs.contains(key)) wcs(key)+1 else 1
  wcs = wcs + (key -> value)
}
println(wcs)
```

4.

``` scala
val in = new java.util.Scanner(new java.io.File("my-file.txt"))
var wcs = scala.collection.immutable.SortedMap[String, Int]()
while(in.hasNext()){
  val key = in.next()
  val value = if (wcs.contains(key)) wcs(key)+1 else 1
  wcs = wcs + (key -> value)
}
println(wcs)
```

5.

``` scala
import scala.collection.JavaConversions.mapAsScalaMap
val in = new java.util.Scanner(new java.io.File("my-file.txt"))
val wcs: scala.collection.mutable.Map[String, Int] = new java.util.TreeMap[String, Int]
while(in.hasNext()){
  val key = in.next()
  wcs(key) = if (wcs.contains(key)) wcs(key)+1 else 1
}
println(wcs)
```

6.

==

7.

``` scala
import scala.collection.JavaConversions.propertiesAsScalaMap
val props: scala.collection.Map[String, String] = System.getProperties()
val maxLen = props.keySet.map(_.length).max
for( (k,v) <- props) {
  print(k)
  print( " " * (maxLen-k.length+4) + " | ")
  print(v)
  println()
}
```

8.

``` scala
def minmax(values: Array[Int]) = (values.min, values.min)
```

9.

``` scala
def lteqgt(values: Array[Int], v: Int) = {
    val eq = values.filter(_ == v).length
    val lt = values.filter(_ < v).length
    val gt = values.length - eq - lt
    (lt, eq, gt)
}
```

10.

``` scala
scala> "Hello".zip("World")
res7: scala.collection.immutable.IndexedSeq[(Char, Char)] = Vector((H,W), (e,o), (l,r), (l,l), (o,d))
```

## 第五章

1.

``` scala
class Counter {
    private var value:BigInt = 0
    def increment() = value += 1
    def current = value
}
```

2.

``` scala
class BankAccount {
    private var privateBalance = 0
    def balance = privateBalance
    def despoit(amount: Int) = privateBalance += amount
    def withdraw(amount: Int) = privateBalance -= amount
}
```

3.

``` scala
class Time(hrs: Int, min: Int) {
    val (hours, minutes) = (hrs, min)
    def before(other: Time) = {
        if (hours < other.hours  || hours == other.hours && minutes < other.minutes) true else false
    }
}
```

4.

``` scala
class Time(hrs: Int, min: Int) {
    private val mins = hrs*60 + min
    def hours = mins/60
    def minutes = mins%60
    def before(other: Time) = {
        if (hours < other.hours  || hours == other.hours && minutes < other.minutes) true else false
    }
}
```

5.

``` scala
import scala.reflect.BeanProperty
class Student(@BeanProperty var name: String, @BeanProperty var id: Long) {
}
```
生成的方法：`getId          getName        id             id_=           name           name_=         setId          setName`

6.

``` scala
class Person(value: Int) {
    private var privateAge = if (value < 0) 0 else value
    def age() = privateAge
    def age_=(newValue: Int) = {
        if (newValue > privateAge) privateAge = newValue
    }
}
```

7.

``` scala
class Person(fullName: String) {
    private var first = fullName.split(" ")(0)
    private var last = fullName.split(" ")(1)
    def firstName = first
    def lastName = last
}
```

8.

``` scala
class Car(val manufacturer: String, val kind: String, val year: Int, var license: String) {
    def this(manufacturer: String, kind: String) = this(manufacturer, kind, -1, "")
    def this(manufacturer: String, kind: String, year: Int) = this(manufacturer, kind, year, "")
    def this(manufacturer: String, kind: String, license: String) = this(manufacturer, kind, -1, license)
}
```

9.

~~

10.

``` scala
class Employee {
    private var privateName = "John Q. Public"
    var salary = 0.0
    def name = privateName
    def this(name: String, salary: Double) {
        this()
        this.privateName = name
        this.salary = salary
    }
}
```

## 第六章

1.

``` scala
object Conversions {
    def inchesToCentimeters(inches: Double) = inches * 2.54
    def gallonsToLiters(gallons: Double) = gallons * 3.78541178
    def milesToKilometers(miles: Double) = miles * 1.609344
}
```

2.

``` scala
abstract class UnitConversion(val unit: Double) {
    def apply(value: Double) = value * unit
}
object InchesToCentimeters extends UnitConversion(2.54)
object GallonsToLiters extends UnitConversion(3.78541178)
object MilesToKilometers extends UnitConversion(1.609344)
```

3.

4.

``` scala
class Point(val x: Double, val y: Double)
object Point {
    def apply(x: Double, y: Double) = new Point(x, y)
}
```

5.

``` scala
object Reverse extends App {
    println(args.reverse.mkString(" "))
}
```
6.

7.

8.

## 第七章

1.

``` scala
package com {
    package horstmann {
        class Who {
            println("I am horstmann")
        }
        package impatient {
            class Hello {
                println("hello")
                new Who
            }
        }
    }
}
package com.horstmann.impatient {
    class HelloAgain {
        println("hello again")
        new Who //this cannot pass
    }
}
```

2.

``` scala
package stupids
package com
class Hello {
    println("hello, my man")
}
```

3.

==

4.

统一概念。

5.

使得方法 giveRaise 可以被包 com 下的类和对象访问。

6.

``` scala
import java.util.{ HashMap => JavaHashMap }
import scala.collection.mutable.{ HashMap => ScalaHashMap }
val jhm = new JavaHashMap[String, Int]
val shm = new ScalaHashMap[String, Int]
jhm.put("a", 1)
jhm.put("b", 2)
val iter = jhm.entrySet().iterator()
while(iter.hasNext()) {
    val entry = iter.next()
    shm.put(entry.getKey(), entry.getValue())
}
println(shm)
```

7.

==

8.

导入 java 和javax 包。这样做并不好，容易产生名称冲突。

9.

``` scala
import java.lang.System
val pwd = Console.readLine("please enter the pwd: ")
if (pwd != "secret") println("incorrect password")
else println("Hello " + System.getProperty("user.name"))
```

10.

``` scala
AbstractMethodError,ArrayIndexOutOfBoundsException,Boolean,Byte,ClassCastException,Cloneable,Double,Error,Exception,Float,IllegalArgumentException,IndexOutOfBoundsException,InterruptedException,Iterable,Long,NullPointerException,NumberFormatException,RuntimeException,Short,StringBuilder,StringIndexOutOfBoundsException,Throwable,UnsupportedOperationException,annotation,ref,reflect
```

### 第八章 - 继承

1.

``` scala
class BankAccount(initialBalance: Double) {
    private var balance = initialBalance
    def deposit(amount: Double) = { balance += amount; balance }
    def withdraw(amount: Double) = { balance -= amount; balance }
}
class CheckingAccount(initialBalance: Double) extends BankAccount(initialBalance) {
    override def deposit(amount: Double) =  super.deposit(amount-1)
    override def withdraw(amount: Double) = super.withdraw(amount+1)
}
```

## 第十章 - 特质

1.

``` scala
    trait RectangleLike {
      def getX(): Double
      def getY(): Double
      def getWidth(): Double
      def getHeight(): Double
      def setFrame(x: Double, y: Double, w: Double, h: Double)
      def translate(dx: Double, dy: Double) = {
        setFrame(getX + dx, getY + dy, getWidth, getHeight)
      }
      def grow(h: Double, v: Double) = {
        setFrame(getX - h, getY - v, getWidth + 2*h, getHeight + 2*v)
      }
    }
```
2.

``` scala
    import java.awt.Point
    class OrderedPoint(x: Int, y: Int) extends Point(x, y) with Ordered[OrderedPoint] {
      def compare(that: OrderedPoint) = {
        if (this.x < that.x || this.x == that.x && this.y < this.y) -1
        else if(this.x == that.x && this.y == that.y) 0
        else 1
      }
    }
```
