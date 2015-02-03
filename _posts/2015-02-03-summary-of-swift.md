---
title: Swift小结
category: way-to-explore
layout: post
---

实习结束后的这段时间里主要是在学Java，成功撸完了《Java编程思想》、《Effective Java》这两本书。收获很大。这两天又玩了下Swift，简单记录下一些点，免得将来忘了。内容来自官方的文档的***Swift Tour***，主要是一些简单的语法。

至于为什么看了Java收获很大，最后却弄了篇Swift的小结出来？没有为什么，有时间就是这么任性。*真实情况是信息量太多消化不过来，太密集的看一个领域的东西看到集中不了注意力我会说吗。还是编程语言简单入门实在点。*

# Hello world!

程序员经典入门语句：注意不需要`;`结尾

    println("Hello, world!")
  

# 变量、常量与值

使用let声明常量

    let hello = "hello, world!"


使用var声明变量

    var str = "Hello, playground"


声明的时候不需要显式的指明类型，类型在第一次赋值的时候由编译器推断
此后对这个变量进行赋值必须使用同一类型的对象(所以这是一门静态语言，只不过编译器能够自动推导类型)
所以执行下面这个语句编译器会报错

    var str = "Hello, playground"
    str = 47

在需要显式的指明类型的场合

    var doubleVal: Double = 70

类型不会自动(隐式)转换为其他的类型，需要进行显式的转换

    let label = "Age: "
    let age = 18
    let ageLabel = label + String(age)

    // 在字符串中包含变量的值可以用更简单的方式
    let ageLabelSimple = "Age: \(age)"

用`[]`创建数组或字典，通过提供下标或键来访问值
    var lis = [
        "I",
        "am",
        "SR1"
    ]

    var dit = [
        "name": "SR1",
        "age": 18
    ]

    lis[0]
    lis[1]

    dit["name"]
    dit["age"]

创建列表或字典的另一种方式

    var lis2 = [String]()
    var dit2 = [String: Float]()

如果类型信息能被推断出来，那么类型可以被省略

    var lis3 = []
    var dit3 = [:]

# 流程控制

使用  `if`、`switch`进行条件判断
使用`for`、`for-in`、`while`、`do-while`进行循环
是否使用`()`把条件括起来是可选的
方法体必须使用`{}`包起来

    let scores = [75, 43, 103, 87, 11]
    var total = 0
    for score in scores {
        if score > 50 {
            total += 3
        } else {
            total += 1
        }
    }
    println(String(total))

在`if`的表达式里，求值结果必须为一个布尔值(`Boolean`)
并不像C、C++里，与0做比较

组合使用`if`和`let`对可能没有赋值的变量进行判断

    var optStr: String? = "Hello"
    optStr == nil

    var optName: String? = "SR1"
    var greeting = "Hello!"
    if let name = optName {
    greeting = "Hello, \(name)"
    }

`switch`支持任意数据类型，不需要显式调用`break`，语句会在执行完当前`case`的语句之后自动退出`switch`

    let vegetable = "pepper"
    switch vegetable {
    case "pepper":
        let vegetableComm = "pepper is good"
    case "cucumber", "watercress":
        let vegetableComm = "cucumber and watercress is good too"
    default:
        let vegetableComm = "Everything is good"
    }

使用一对变量，配合`for-in`对字典里的元素进行迭代
字典是一个没有被排序的集合，所以迭代根据键的字典序进行迭代

    let numdit = [
        "Prime": [2, 3, 5, 7, 11, 13],
        "Fibonacci": [1, 1, 2, 3, 5, 8],
        "Square": [1, 4, 9, 16, 25, 36]
    ]
    var largest = 0
    for (kind, nums) in numdit {
        for num in nums {
            if num > largest {
                largest = num
            }
        }
    }
    println(largest)

使用`while`来循环一个代码块
直到指定的条件不成立
使用`do-while`循环来保证代码块至少执行一次

    var n = 2
    while n < 100 {
        n = n * 2
    }
    println(n)

    var m = 2
    do {
        m = m * 2
    } while m < 100
    println(m)

`for`循环有两种用法：
Python类似的用法：使用`..<`创建一个指定范围的索引列表

    var fir4Loop = 0
    for i in 0..<4 {
        fir4Loop += i
    }
    println(fir4Loop)


一般的for循环用法

    var sed4Loop = 0
    for var i = 0; i < 4; ++i {
        sed4Loop += i
    }
    println(sed4Loop)

# 方法和闭包

使用`func`声明一个方法
调用一个方法只需要指定方法名在加上一个列表(用圆括号包裹)
使用`->`分割参数列表和函数返回类型

    func greet(name: String, day: String) -> String {
        return "Hello \(name), today is \(day)"
    }
    
    greet("SR1", "Sunday")

使用元组来创建一个组合值
比如返回从函数里返回多个值
元组里的元素可以通过名字或数字来访问

    func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
        var min = scores[0]
        var max = scores[1]
        var sum = 0
    
        for score in scores {
            if score > max {
                max = score
            } else if score < min {
                min = score
            }
            sum += score
        }
        return (min, max, sum)
    }
    
    let statistics = calculateStatistics([5, 3, 100, 3, 9])
    statistics.sum
    statistics.2

函数可以接受可变数量的参数，并把他们收集到一个数组里

    func sumOf(numbers: Int...) -> Int {
        var sum = 0
        for num in numbers {
            sum += num
        }
        return sum
    }
    
    sumOf()
    sumOf(1, 2, 3, 4)

方法可以内嵌
内嵌的方法可以访问到外层方法里的变量
可以通过使用内嵌的方法来组织长/复杂的方法

    func returnFifteen() -> Int {
        var y = 10
        func add() {
            y += 5
        }
        add()
        return y
    }

方法是一级类型
这意味着一个方法可以返回另一个方法作为返回值

    func makeIncrementer() -> (Int -> Int) {
        func addOne(number: Int) -> Int {
            return 1 + number
        }
        return addOne
    }
    var increment = makeIncrementer()
    increment(7)

一个方法可以将另一个方法作为参数

    func hasAnyMatches(list: [Int], condition: Int -> Bool) -> Bool {
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
    
    var numbers = [20, 19, 15, 12]
    hasAnyMatches(numbers, lessThanTen)

函数可以看作是闭包的一种特例
闭包中的代码可以访问在方法定义的时候可以访问的变量和方法
就算函数执行的时候已经脱离了这个环境
内嵌函数就是这样的一个例子
定义一个匿名的闭包，只需要将代码用`{}`包裹起来
使用`in`来分割参数和返回类型

    numbers.map({
        (number: Int) -> Int in
        let result = 3 * number
        return result
    })

有好几种方式可以写出简洁的闭包
当一个闭包的类型是已知的，比如一个委托的回调函数
这时可以省略参数类型，返回类型，或者全部省略
单一语句的闭包函数隐式的返回这个语句的值

    let mappedNumbers = numbers.map({ number in 3 * number})
    mappedNumbers

也可以通过数字来访问参数，而不通过名字
这在简短的闭包函数里非常有用
一个闭包作为一个函数的最后一个参数时
可以直接在参数的后面直接出现

    let sortedNumbers = sorted(numbers) { $0 > $1 }
    sortedNumbers

下面和上面的写法是等价的

    let sortedNumberss = sorted(numbers, { $0 > $1 })
    sortedNumberss

# 对象和类

使用class关键字随后跟上类名来创建一个类
类里的属性的声明方式和声明一个常量和变量一样
只不过属性存在类的上下文里
方法与函数的定义一样

    class Shape {
        var numberOfSides = 0
        func simpleDescription() -> String {
            return "A shape with \(numberOfSides) sides"
        }
    }

创建一个类的实例

    var shape = Shape()
    shape.numberOfSides = 7
    var shapeDescription = shape.simpleDescription()
    println(shapeDescription)

使用`init`方法来创建构造函数

    class NamedShape {
        var numberOfSides: Int = 0
        var name: String
    
        init(name: String) {
            self.name = name
        }
    
        func simpleDescription() -> String {
            return "A shape with \(numberOfSides) sides."
        }
    }

需要注意的是，在构造器里，self被用于区分name属性和name局部变量
传递给构造器的参数类似方法调用里的传参，只不过这个方法是初始化的时候调用的
每个属性的值都需要初始化(在声明的时候，或者构造的时候)
使用`deinit`来创建析构器

子类通过在类名后面加上父类的名字来继承
任意标准根类对子类化没有任何要求
根据需要可以包含或者省略父类

在方法前加上override来覆盖父类的方法
编译器会检查这个，如果没有添加override，编译器会将之视为一个错误
倘若使用了override的方法实际上并没有覆盖父类的方法，编译器也会将之是为一个错误

    class Square: NamedShape {
        var sideLength: Double
    
        init(sideLength: Double, name: String) {
            self.sideLength = sideLength
            super.init(name: name)
            numberOfSides = 4
        }
    
        func area() -> Double {
            return sideLength * sideLength
        }
    
        override func simpleDescription() -> String {
            return "A square with side of length \(sideLength)."
        }
    }
    
    let test = Square(sideLength: 5.2, name: "My test square")
    test.area()
    test.simpleDescription()

对于被存储的简单的属性，可以设置一个`getter`和`setter`

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
            return "An equilateral triangle with sides of length \(sideLength)."
        }
    }
    var triangle = EquilateralTriangle(sideLength: 3.1, name: "a triangle")
    triangle.perimeter
    triangle.perimeter = 9.9
    triangle.sideLength

注意初始化步骤
1.为子类的属性赋值
2.调用父类的构造器
3.改变父类定义的属性的值

若不需要计算属性的值，但仍然需要在赋值前/后运行其他的代码的话
使用willSet和didSet

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

函数和方法有一个非常重要的不同
函数的参数名仅仅在函数的内部被使用
而方法的参数名在调用的时候也被使用(除了第一个参数)

    class Counter {
        var count: Int = 0
        func incrementBy(amount: Int, numberOfTimes times: Int) {
            count += amount * times
        }
    }
    var counter = Counter()
    counter.incrementBy(2, numberOfTimes: 7)

使用可选值时，在操作(方法、属性、subscripting等)前面加个`?`。
如果`?`前面的值为`nil`，那么`?`之后的所有操作都会被忽略，这个表达式的值为`nil`
否则可选的值将会被解包，`?`之后的所有操作都会被执行
在这两种情况下，整个表达式的值将是可选值

    let optSquare: Square? = Square(sideLength: 2.5, name: "optional square")
    let sideLength = optSquare?.sideLength

# 枚举和结构

使用`enum`关键字来创建枚举
与类相似，枚举也可以定义方法

    enum Rank: Int {
        case Ace = 1
        case Two, Three, Four, Five, Six, Seven,
             Eight, Nine, Ten
        case Jack, Queen, King
        
        func simpleDecription() -> String {
            switch self {
            case .Ace:
                return "ace"
            case .Jack:
                return "jack"
            case .Queen:
                return "queen"
            default:
                return String(self.rawValue)
            }
        }
    }
    let ace = Rank.Ace
    let aceRawVal = Rank.Ace.rawValue

在上面的例子中，枚举类的裸值的类型是`Int`
因此只需要指出第一个枚举的值，其他值会按顺序赋给每一个枚举
同样的可以使用字符串或者浮点数作为裸值类型
使用`.rawValue`来访问一个枚举的裸值

使用`init?(rawValue:)`构造器来从一个裸值里创建一个枚举

    if let convertedRand = Rank(rawValue: 3) {
        let threeDescription = convertedRand.simpleDecription()
    }

枚举成员的值是真实的值，而不是另一种表示裸值的方式
在某些场合，如果不存在有意义的裸值，那么可以不提供这个值

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
    let heart = Suit.Hearts
    let heartDescription = heart.simpleDescription()

需要注意的是，在赋值的时候，枚举值需要使用全名(`Suit.Hearts`)
因为被赋值的变量无法知道具体的类型
而在内部的`swith`标签里，枚举值可以简写
在类型已经明确的场合，就能使用简写形式

使用`struct`创建结构
结构支持类的所有行为，包括构造器和方法
它们之间的不同在于传递数据时，结构传递的是值，而类传递的是引用

    struct Card {
        var rank: Rank
        var suit: Suit
        func simpleDescription() -> String {
            return "The \(rank.simpleDecription()) of \(suit.simpleDescription())"
        }
    }
    
    let threeOfSpades = Card(rank: .Three, suit: .Spades)
    let threeOfSpadesDescription = threeOfSpades.simpleDescription()

枚举成员的实例可以拥有相关的值
同一个枚举成员的不同实例可以拥有不同的关联值
这些值在创建实例的时候提供
关联值和裸值有所区别
裸值再同一个枚举成员的所有实例中是一样的，它是在定义枚举成员的时候指定的

    enum ServerResponse {
        case Result(String, String)
        case Error(String)
    }
    
    let success = ServerResponse.Result("6:00 am", "8:09 pm")
    let failure = ServerResponse.Error("Out of cheese")
    
    switch success {
    case let .Result(sunrise, sunset):
        let serverResponse = "Sunrise is at \(sunrise) and sunset in at \(sunset)"
    case let .Error(error):
        let serverResponese = "Failure... \(error)"
    }

# 接口(Protocols)和扩展(Extensions)
 
使用`protocol`定义一个接口

    protocol ExampleProtocol {
        var simpleDescription: String { get }
        mutating func adjust()
    }

类、枚举、结构都能实现接口

    class SimpleClass: ExampleProtocol {
        var simpleDescription: String = "A very simple class"
        var anotherProperty: Int = 69105
        func adjust() {
            simpleDescription += " Now 100% adjusted."
        }
    }
    var a = SimpleClass()
    a.adjust()
    let aDescription = a.simpleDescription

    struct SimpleStruct: ExampleProtocol {
        var simpleDescription: String = "A simple struct"
        mutating func adjust() {
            simpleDescription += " (adjusted)"
        }
    }
    
    var b = SimpleStruct()
    b.adjust()
    let bDescription = b.simpleDescription

在结构的方法声明中使用了`mutating`关键字，表示这个方法修改了结构
类的方法声明没有使用这个关键字，因为类本来就可以修改自身

使用`extension`来为已经存在的类型添加功能，如新方法或者属性
可以使用`extension`来为现有的类增加实现新的接口
(不会产生新的子类，而是为原有的类添加功能)

    extension Int: ExampleProtocol {
        var simpleDescription: String {
            return "The number \(self)"
        }
        mutating func adjust() {
            self += 42
        }
    }
    
    7.simpleDescription

接口的名字可以用来作为类型表示
比如一个集合，里面每个对象来自不同的类
但它们实现了同一个接口
当你把一个对象用接口类型表示时
接口外的方法就无法调用了(属于类而不属于接口的方法在这个时候不可见)

    let protocolVal: ExampleProtocol = a
    protocolVal.simpleDescription
    // protocolVal.anotherProperty

# 泛型

在尖括号`<>`里声明一个泛型替代名，把类或方法变成泛型的

    func repeat<Item>(item: Item, times: Int) -> [Item] {
        var result = [Item]()
        for i in 0..<times {
            result.append(item)
        }
        return result
    }
    repeat("knock", 4)

泛型可以用在方法、函数、类、枚举、结构中

    enum OptValue<T> {
        case None
        case Some(T)
    }
    var possibleInteger: OptValue<Int> = .None
    possibleInteger = .Some(100)

使用where关键字来指定需要满足的条件列表

    func anyCommonElements<T, U where 
        T: SequenceType, U: SequenceType,
        T.Generator.Element: Equatable,
        T.Generator.Element == U.Generator.Element>
        (lhs: T, rhs: U) -> Bool {
        for lhsItem in lhs {
            for rhsItem in rhs {
                if lhsItem == rhsItem {
                    return true
                }
            }
        }
        return false
    }
    anyCommonElements([1, 2, 3], [3])

在比较简单的场合下，可以用使用简写的方式，如
`<T: Equatable>` 等价于 `<T where T: Equatable>`

That's all, But not ALL.

> Written with [StackEdit](https://stackedit.io/).