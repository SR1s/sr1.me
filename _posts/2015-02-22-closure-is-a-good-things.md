---
title: 闭包是个好东西！
category: way-to-explore
layout: post
---

# 闭包
闭包这个概念最初是在一些关于JavaScript的文章里看到的，当时没懂怎么懂，作为一个Java出身的程序员，方法跟类不是同一级别的东西，固然很难理解，以至于即便使用了Python，也不曾想过闭包有神马用处，这就是所谓的思维局限吧。关于闭包的是什么，可以看下这篇文章《[学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)》

# 如何用闭包写出优雅的代码
关于闭包，以及闭包的用处，看完上面那篇阮一峰大大的文章就能明白了。这里想说的是，如何用闭包来写优雅的代码。这个例子是这几天看斯坦福的iOS教程(Swift)学到的，这种写法让我惊为天人。

## 1. 最“通俗”的写法

这是一个计算`numberStack`栈顶两个元素的加减乘除结果的小程序，对一个程序员来说，最“通俗”的写法大概是这样。

    var numberStack = Array<Double>()
    
    var result = 0.0
    
    func operate(operation: String) {
        switch operation {
        case "×":
            if numberStack.count >= 2 {
                result = numberStack.removeLast() * numberStack.removeLast()
            }
        case "÷":
            if numberStack.count >= 2{
                var num2 = numberStack.removeLast()
                var num1 = numberStack.removeLast()
                result = num1 / num2
            }
        case "+":
            if numberStack.count >= 2{
                result = numberStack.removeLast() + numberStack.removeLast()
            }
        case "-":
            if numberStack.count >= 2{
                var num2 = numberStack.removeLast()
                var num1 = numberStack.removeLast()
                result = num1 - num2
            }
        default:
            break
        }
    }

但这样的代码不好维护：每个`case`对应的操作都挤在一起，操作简单还好说，要是长了，这个函数的代码行数会极速膨胀。

## 2. Java程序员的版本

    var numberStack = Array<Double>()

    var result = 0.0

    func operate(operation: String) {
        switch operation {
        case "×":
            multiply()
        case "÷":
            devide()
        case "+":
            plus()
        case "-":
            minus()
        default:
            break
        }
    }

    func multiply() {
        if numberStack.count >= 2 {
            result = numberStack.removeLast() * numberStack.removeLast()
        }
    }

    func devide() {
        if numberStack.count >= 2{
            var num2 = numberStack.removeLast()
            var num1 = numberStack.removeLast()
            result = num1 / num2
        }
    }

    func plus() {
        if numberStack.count >= 2{
            result = numberStack.removeLast() + numberStack.removeLast()
        }
    }

    func minus() {
        if numberStack.count >= 2{
            var num2 = numberStack.removeLast()
            var num1 = numberStack.removeLast()
            result = num1 - num2
        }
    }

作为一个Java程序员，代码规范一般都会这个要求：将`switch`里的每个`case`都抽取到一个方法里去。这样大概是Java程序员唯一能做到的最简单的改进了，采用策略模式的话代码不知要膨胀多少出来。但我们不禁要问问自己：这样就够了吗？(鹅厂人的口头禅？)

## 3. 有闭包，you can do better

    var numberStack = Array<Double>()

    var result = 0.0

    func operate(operation: String) {
        switch operation {
        case "×":
            performOperation(multiply)
        case "÷":
            performOperation(devide)
        case "+":
            performOperation(plus)
        case "-":
            performOperation(minus)
        default:
            break
        }
    }

    func performOperation(operation: (Double, Double) -> Double) {
        if numberStack.count >= 2 {
            result = operation(numberStack.removeLast(), numberStack.removeLast())
        }
    }

    func multiply(num1: Double, num2: Double) -> Double {
        return num1 * num2
    }

    func devide(num1: Double, num2: Double) -> Double {
        return num2 / num1
    }

    func plus(num1: Double, num2: Double) -> Double {
        return num1 + num2
    }

    func minus(num1: Double, num2: Double) -> Double {
        return num2 - num1
    }
    
有了闭包，代码可以写得更紧凑一点。虽然咋看好像没什么差别，但实际上代码的可复用程度以及可维护性都棒了。在这种情况下实际上已经实现了策略模式，而编写的代码却远远比Java少了很多，这在之前是无法想象的。但这就完了吗？No！借助Swift的语言特性(参见前一篇[日志](http://sr1.me/way-to-explore/2015/02/03/summary-of-swift.html))，这还能更精简。

## 4. Swift大法好

    var numberStack = Array<Double>()

    var result = 0.0

    func operate(operation: String) {
        switch operation {
        case "×":
            performOperation() { $0 * $1 }
        case "÷":
            performOperation() { $1 / $0 }
        case "+":
            performOperation() { $0 + $1 }
        case "-":
            performOperation() { $1 - $0 }
        default:
            break
        }
    }

    func performOperation(operation: (Double, Double) -> Double) {
        if numberStack.count >= 2 {
            result = operation(numberStack.removeLast(), numberStack.removeLast())
        }
    }
    
借助`Swift`语言原生具有的特性，原本的程序能用这么点代码来写成。虽然实现还是在`Switch`代码块里完成，但在这种情况(需求)下，这已经是非常非常少的代码了，无法再进行优化，而可维护性和可复用性却不变！

当然这里也有不好的地方：比如你需要知道`$0`和`$1`分别对应闭包方法里的参数是什么意义，否则最开始看的话会比较困惑。而且这种简化写法需要你对这种语言特性有所了解和熟悉，咋一看还是有阅读成本的，但等你适应了这种特性，阅读起来就会感觉非！常！爽！妈蛋我快要爱上这种高逼格的语言了，谁来拉我一把，开发Android去啊！

## 5. That's all, but not ALL.

> Written with [StackEdit](https://stackedit.io/).