---
layout: post
title: a tour on Swift Language Tour
---

> 本文根据 Apple Inc. 官方的 Swift Tour 总结得出。

> 如有任何异议，请参考官方文档。

## 简单值（simple values）

1. 使用 `let` 定义只能赋值一次的常量，使用 `var` 定义可多次赋值的变量。
   （类似于 Scala 中的 val/var）
2. 在可推导得知的情况下，类型声明可省略。（Scala 即视感）
3. 类型声明放在变量后，用 `:` 隔开。（Scala 即视感）

   ```
   var myVariable = 42
   myVariable = 50
   let myConstant = 42

   let implicitInteger = 70
   let implicitDouble = 70.0
   let explicitDouble: Double = 70
   ```

4. 值无法进行隐式转换。（这一点和 Scala 差别很大）

   ```
   let label = "The width is "
   let width = 94
   let widthLabel = label + String(width)
   ```

5. 使用 `\()` 进行字符串中的变量解释。

   ```
   let apples = 3
   let oranges = 5
   let appleSummary = "I have \(apples) apples."
   let fruitSummary = "I have \(apples + oranges) pieces of fruit."
   ```

6. 使用 `[]` 定义数组和哈希，并使用下标和键访问。

   ```
   var shoppingList = ["catfish", "water", "tulips", "blue paint"]
   shoppingList[1] = "bottle of water"
   var occupations = [
       "Malcolm": "Captain",
       "Kaylee": "Mechanic",
   ]
   occupations["Jayne"] = "Public Relations"
   ```

   使用初始化语法创建空数组和哈希。

   ```
   let emptyArray = String[]()
   let emptyDictionary = Dictionary<String, Float>()
   ```

   在类型可推导的情况下，也可以直接用 `[]` 表示空数组， `[:]` 表示空哈希。

   ```
   shoppingList = []
   ```

## 控制流（Control Flow）

1. `if` 和 `switch` 作条件语句， `for/in` `for` `while` `do/while` 作循环语句。
   条件头和循环头的小括号是可选的，而条件体和循环体的大括号是必须的。

   ```
   let individualScores = [75, 43, 103, 87, 12]
   var teamScore = 0
   for score in individualScores {
       if score > 50 {
           teamScore += 3
       } else {
           teamScore += 1
       }
   }
   teamScore
   ```

   ```
   let interestingNumbers = [
       "Prime": [2, 3, 5, 7, 11, 13],
        "Fibonacci": [1, 1, 2, 3, 5, 8],
        "Square": [1, 4, 9, 16, 25],
    ]
    var largest = 0
    for (kind, numbers) in interestingNumbers {
        for number in numbers {
            if number > largest {
                largest = number
            }
        }
    }
    largest
   ```

   条件必须是一个布尔值。

2. 可以用 `if/let` 处理可选值。

   > An optional value either contains a value or contains nil to indicate that the value is missing. Write a question mark (?) after the type of a value to mark the value as optional.

   > ```
   > var optionalString: String? = "Hello"
   > optionalString = nil
   > ```
   > 用这样的方式来创建 Optional 看起来很具有可读性，
   > 但我不喜欢这样的语法糖（有点 hacking 的味道），增加了记忆成本。

   ```
   var optionalString: String? = "Hello"
   optionalString == nil

   var optionalName: String? = "John Appleseed"
   var greeting = "Hello!"
   if let name = optionalName {
       greeting = "Hello, \(name)"
   }
   ```

   如果可选值是 `nil` ，那 `if` 语句就是 `false` ，
   否则可选值中的值会被解包，赋值给 `let` 后的常量， `if` 代码块就可以使用这个常量值了。

3. Swift 把 switch expression 做对了，有了模式匹配功能，但需要 let binding，
   和 Scala 中的 match/case 相比，略显复杂。

   ```
   let vegetable = "red pepper"
   switch vegetable {
   case "celery":
       let vegetableComment = "Add some raisins and make ants on a log."
   case "cucumber", "watercress":
       let vegetableComment = "That would make a good tea sandwich."
   case let x where x.hasSuffix("pepper"):
       let vegetableComment = "Is it a spicy \(x)?"
   default:
       let vegetableComment = "Everything tastes good in soup."
   }
   ```

4. `while` 的用法没有什么特殊之处。
5. 在循环中跟踪一个索引：
   - 使用 `..` 生成一个 range。
   - 另一种方法是使用类似于 C 的 for 循环形式。

   ```
   var firstForLoop = 0
   for i in 0..3 {
       firstForLoop += i
   }
   firstForLoop

   var secondForLoop = 0
   for var i = 0; i < 3; ++i {
       secondForLoop += 1
   }
   secondForLoop
   ```

   > Use `..` to make a range that omits its upper value, and use `...` to make a range that includes both values.

   > 这个完全违背先前的认知，为何不与其他有类似功能的语言如 ruby、python 保持一致？

## 函数和闭包

1. 使用 `func` 定义函数，在尾部用 `->` 表明函数返回类型。
2. 用 `...` 收集可变个数的参数到一个数组中，类似于 Java。

   ```
   func sumOf(numbers: Int...) -> Int {
       var sum = 0
       for number in numbers {
           sum += number
       }
       return sum
   }
    sumOf()
    sumOf(42, 597, 12)
   ```

3. 函数是闭包的一种特殊形式。
4. 函数可以嵌套，是一等公民，可以被返回，可以被当参数传递。

   ```
   func makeIncrementer() -> (Int -> Int) {
       func addOne(number: Int) -> Int {
           return 1 + number
       }
       return addOne
   }
   var increment = makeIncrementer()
   increment(7)
   ```

   ```
   func hasAnyMatches(list: Int[], condition: Int -> Bool) -> Bool {
       for item in list {
           if condition(item) {
               return true
           }
       }
       return false
   }
   func lessThanTen(number: Int) -> Bool {
       return number < 10
   }
   var numbers = [20, 19, 7, 12]
   hasAnyMatches(numbers, lessThanTen)
   ```

5. 用 `{}` 定义匿名的闭包，用 `in` 将闭包体与参数和返回值分隔开。

   ```
   numbers.map({ (number: Int) -> Int in
       let result = 3 * number
       return result
   })
   ```

6. 为了让闭包的创建更加简单， Swift 提供了几个语法糖，例如：省略参数类型，返回类型。

   但另外一个语法糖，我不太喜欢： *当闭包只有一个语句时，默认返回该语句的值* 。
   又是一个记忆成本，为何函数不直接默认返回最后一条语句的值？

   ```
   numbers.map({ number in 3 * number })
   ```

   同时，可以用数字代替名字来引用参数，和 Scala 的 `_n` 语法很像...

   ```
   sort([1, 3, 4, 12, 2]) { $0 > $1 }
   ```

## 对象和类

1. 使用 `class` 关键字， 直接在类中定义属性和方法。
2. 调用时，直接用类名实例化。
3. `init` 作为初始化方法， `deinit` 作为解初始化方法。
4. 用 `self` 自引用。
5. 类继承用分号表示。
6. 子类重写父类方法须用 `override` 显示指定，否则编译器报错。
7. 用 `super` 引用父类。

   ```
   class Square: NamedShape {
       var sideLength: Double
       init(sideLength: Double, name: String) {
           self.sideLength = sideLength
           super.init(name: name)
           numberOfSides = 4
       }
       func area() ->  Double {
           return sideLength * sideLength
       }
       override func simpleDescription() -> String {
           return "A square with sides of length \(sideLength)."
       }
   }
   let test = Square(sideLength: 5.2, name: "my test square")
   test.area()
   test.simpleDescription()
   ```

8. 属性可以有 `getter` 、 `setter` 方法，语法从 C井 借鉴，
   还可以有 `willSet` 和 `didSet` 这样的钩子方法。

   ```
   class EquilateralTriangle: NamedShape {
       var sideLength: Double = 0.0

       init(sideLength: Double, name: String) {
           self.sideLength = sideLength
           super.init(name: name)
           numberOfSides = 3
       }

       var perimeter: Double {
       get {
           return 3.0 * sideLength
       }
       set {
           sideLength = newValue / 3.0
       }
       }

       override func simpleDescription() -> String {
           return "An equilateral triagle with sides of length \(sideLength)."
       }
   }
   var triangle = EquilateralTriangle(sideLength: 3.1, name: "a triangle")
   triangle.perimeter
   triangle.perimeter = 9.9
   triangle.sideLength
   ```

   ```
   class TriangleAndSquare {
       var triangle: EquilateralTriangle {
       willSet {
           square.sideLength = newValue.sideLength
       }
       }
       var square: Square {
       willSet {
           triangle.sideLength = newValue.sideLength
       }
       }
       init(size: Double, name: String) {
           square = Square(sideLength: size, name: name)
           triangle = EquilateralTriangle(sideLength: size, name: name)
       }
   }
   var triangleAndSquare = TriangleAndSquare(size: 10, name: "another test shape")
   triangleAndSquare.square.sideLength
   triangleAndSquare.triangle.sideLength
   triangleAndSquare.square = Square(sideLength: 50, name: "larger square")
   triangleAndSquare.triangle.sideLength
   ```

9. 方法与函数有一个重要的区别：方法定义时的参数名在调用时也会使用（除了第一个参数）,
   而在函数定义中使用参数名时，可以另外指定一个新的名字，默认这两者是一样的。
   （不知道这个语法有什么用处，徒增复杂度）

   ```
   class Counter {
       var count: Int = 0
       func incrementBy(amount: Int, numberOfTimes times: Int) {
           count += amount * times
       }
   }
   var counter = Counter()
   counter.incrementBy(2, numberOfTimes: 7)
   ```

10. 用 `?` 对可选值进行 `nil` 检测，记得没错的话，应该是从 Groovy 借鉴的。

    ```
    let optionalSquare: Square? = Square(sideLength: 2.5, name: "optional square")
    let sideLength = optionalSquare?.sideLength
    ```

在对象和类这里，Swift 做对了很多事情。

## 枚举和结构

1. 使用 `enum` 创建枚举，枚举也可以有方法与之关联
2. 使用 `toRaw` 和 `fromRaw` 进行转换
3. 当枚举值类型确定的时候，可以使用缩减写形式，比如 `Suit.Heart` 的简写 `.Heart`

   ```
   enum Rank: Int {
       case Ace = 1
       case Two, Three, Four, Five, Six, Seven, Eight, Nine, Ten
       case Jack, Queen, King
       func simpleDescription() -> String {
           switch self {
           case .Ace:
               return "ace"
           case .Jack:
               return "jack"
           case .Queen:
               return "queen"
           case .King:
               return "king"
           default:
               return String(self.toRaw())
           }
       }
   }
   let ace = Rank.Ace
   let aceRawValue = ace.toRaw()
   ```

4. 无需为枚举成员指定一个实际值。

   ```
   enum Suit {
       case Spades, Hearts, Diamonds, Clubs
       func simpleDescription() -> String {
           switch self {
           case .Spades:
               return "spades"
           case .Hearts:
               return "hearts"
           case .Diamonds:
               return "diamonds"
           case .Clubs:
               return "clubs"
           }
       }
   }
   let hearts = Suit.Hearts
   let heartsDescription = hearts.simpleDescription()
   ```

5. 使用 `struct` 创建结构，结构和类最重要的区别是，结构是值传递的，而类是引用传递

   ```
   struct Card {
       var rank: Rank
       var suit: Suit
       func simpleDescription() -> String {
           return "The \(rank.simpleDescription()) of \(suit.simpleDescription())"
       }
   }
   let threeOfSpades = Card(rank: .Three, suit: .Spades)
   let threeOfSpadesDescription = threeOfSpades.simpleDescription()
   ```


6. 枚举成员可以有关联值（注意与实际值的区别），在创建的时候指定... （Scala case class 的即视感）

   ```
   enum ServerResponse {
       case Result(String, String)
        case Error(String)
    }

    let success = ServerResponse.Result("6:00 am", "8:09 pm")
    let failure = ServerResponse.Error("Out of cheese.")
    switch success {
        case let .Result(sunrise, sunset):
            let serverResponse = "Sunrise is at \(sunrise) and sunset is at \(sunset)."
        case let .Error(error):
            let serverResponse = "Failure...  \(error)"
    }
   ```

## 协议和扩展（Protocols and Extensions）

1. 使用 `protocol` 声明协议（类、枚举、结构都支持协议）。

   ```
   protocol ExampleProtocol {
       var simpleDescription: String { get }
       mutating func adjust()
   }
   ```

   关键字 `mutating` 表示该方法会有修改动作。
   类无需此关键字，类中的方法总是可以修改这个类。
   （ `mutating` 关键字给我天外来物的感觉，说不上好也说不上坏）

2. 使用 `extension` 给已存在类型添加功能。

   ```
   extension Int: ExampleProtocol {
       var simpleDescription: String {
           return "The number \(self)"
       }
        mutating func adjust() {
            self += 42
        }
   }
   7.simpleDescription
   ```

3. 可以像使用其他类型一样使用一个协议，但协议之外的特殊定义不可用。

   ```
   let protocolValue: ExampleProtocol = a
   protocolValue.simpleDescription
   // protocolValue.anotherProperty  // Uncomment to see the error
   ```

## 泛型（Generics）

1. 使用 `<>` ，函数、方法、类、枚举、结构都可以泛型。
2. 使用 `where` 指定类型参数的范围，简单情况下， 可以省略 `where`。（Scala 泛型的即视感）

   ```
   func anyCommonElements <T, U where
       T: Sequence,
       U: Sequence,
       T.GeneratorType.Element: Equatable,
       T.GeneratorType.Element == U.GeneratorType.Element
       > (lhs: T, rhs: U) -> Bool {
       // body
   }
   ```

   ```
   func exampleGenericFunction<T: Equatable>(a: T, b: T)
   ```


## 总结

一句话：博采众长的 Swift，借鉴别人的东西都很好，自己添加的特性有待时间检验。

（其实特性也不多... 这不是在黑 Swift！）
