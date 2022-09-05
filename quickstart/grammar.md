---
date: 2022-09-05T14:00:02+08:00  # 创建日期
author: "Rustle Karl"  # 作者

title: "Kotlin 基础语法"  # 文章标题
url:  "posts/kotlin/quickstart/grammar"  # 设置网页永久链接
tags: [ "kotlin", "grammar" ]  # 标签
categories: [ "Kotlin 学习笔记" ]  # 分类

toc: true  # 目录
draft: true  # 草稿
---

- [包定义和引入包](#包定义和引入包)
- [程序入口](#程序入口)
- [函数](#函数)
  - [标注返回值](#标注返回值)
  - [编译器推断](#编译器推断)
  - [无返回值](#无返回值)
- [变量](#变量)
  - [只读](#只读)
  - [可赋值](#可赋值)
- [类和实例](#类和实例)
  - [继承](#继承)
- [字符串格式化](#字符串格式化)
- [if 条件表达式](#if-条件表达式)
- [循环表达式](#循环表达式)
  - [for](#for)
  - [while](#while)
- [when 条件表达式](#when-条件表达式)
- [区间](#区间)
- [集合](#集合)
- [在 Kotlin 中使用可空类型](#在-kotlin-中使用可空类型)
  - [声明非空变量](#声明非空变量)
  - [声明一个可空类型](#声明一个可空类型)
  - [检查一个 val 变量是否为空](#检查一个-val-变量是否为空)
  - [使用安全调用操作符](#使用安全调用操作符)
  - [安全调用与 Elvis 组合使用](#安全调用与-elvis-组合使用)
  - [安全转换操作符](#安全转换操作符)
- [打印不同的进制](#打印不同的进制)

## 包定义和引入包

```kotlin
package my.demo

import kotlin.text.*
```

## 程序入口

```kotlin
fun main() {
    println("Hello world!")
}
```

```kotlin
fun main(args: Array<String>) {
    println(args.contentToString())
}
```

## 函数

### 标注返回值

```kotlin
fun sum(a: Int, b: Int): Int {
    return a + b
}
```

### 编译器推断

```kotlin
fun sum(a: Int, b: Int) = a + b
```

### 无返回值

```kotlin
fun printSum(a: Int, b: Int) {
    println("sum of $a and $b is ${a + b}")
}
```

## 变量

### 只读

```kotlin
val a: Int = 1  // immediate assignment
val b = 2   // `Int` type is inferred
val c: Int  // Type required when no initializer is provided
c = 3       // deferred assignment
```

### 可赋值

```kotlin
var x = 5 // `Int` type is inferred
x += 1
```

## 类和实例

```kotlin
class Rectangle(var height: Double, var length: Double) {
    var perimeter = (height + length) * 2
}
```

```kotlin
val rectangle = Rectangle(5.0, 2.0)
println("The perimeter is ${rectangle.perimeter}")
```

### 继承

```kotlin
open class Shape

class Rectangle(var height: Double, var length: Double): Shape() {
    var perimeter = (height + length) * 2
}
```

## 字符串格式化

```kotlin
var a = 1
// simple name in template:
val s1 = "a is $a" 

a = 2
// arbitrary expression in template:
val s2 = "${s1.replace("is", "was")}, but now is $a"
```

## if 条件表达式

```kotlin
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
```

```kotlin
fun maxOf(a: Int, b: Int) = if (a > b) a else b
```

## 循环表达式

### for

```kotlin
val items = listOf("apple", "banana", "kiwifruit")
for (item in items) {
    println(item)
}
```

```kotlin
val items = listOf("apple", "banana", "kiwifruit")
for (index in items.indices) {
    println("item at $index is ${items[index]}")
}
```

### while

```kotlin
val items = listOf("apple", "banana", "kiwifruit")
var index = 0
while (index < items.size) {
    println("item at $index is ${items[index]}")
    index++
}
```

## when 条件表达式

```kotlin
fun describe(obj: Any): String =
    when (obj) {
        1          -> "One"
        "Hello"    -> "Greeting"
        is Long    -> "Long"
        !is String -> "Not a string"
        else       -> "Unknown"
    }
```

## 区间

```kotlin
val x = 10
val y = 9
if (x in 1..y+1) {
    println("fits in range")
}
```

```kotlin
val list = listOf("a", "b", "c")

if (-1 !in 0..list.lastIndex) {
    println("-1 is out of range")
}
if (list.size !in list.indices) {
    println("list size is out of valid list indices range, too")
}
```

```kotlin
for (x in 1..5) {
    print(x)
}
```

```kotlin
for (x in 1..10 step 2) {
    print(x)
}
println()
for (x in 9 downTo 0 step 3) {
    print(x)
}
```

## 集合

```kotlin
for (item in items) {
    println(item)
}
```

```kotlin
when {
    "orange" in items -> println("juicy")
    "apple" in items -> println("apple is fine too")
}
```

```kotlin
val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
fruits
    .filter { it.startsWith("a") }
    .sortedBy { it }
    .map { it.uppercase() }
    .forEach { println(it) }
```

## 在 Kotlin 中使用可空类型

定义不带问号的变量类型。可空类型与安全调用操作符(?.)以及Elvis操作符(?:)结合使用.

Kotlin最有魅力的特征可能就是它消除了几乎所有可能的空值。在Kotlin中如果你定义的变量不在尾部添加问号，则编译器将要求该值不为空。

### 声明非空变量

```kotlin
var name: String

name = "Kotlin"
// name = null
```

将 name 变量声明为 String 类型意味着无法将其赋值为空，否则代码无法编译。

如果你想让一个变量可空，可以在类型声明的后面加一个问号。

```kotlin
var name: String?
```

### 声明一个可空类型

```kotlin
class Person(
    val first: String,
    val middle: String?,
    val last: String,
)

var p1 = Person("Joanne", null, "Rowling")
var p2 = Person("North", null, "West")
```

在展示的 Person 类中，即使 middle 参数为空，你仍然必须为其提供一个值。

当你尝试在表达式中使用可空的变量时，会变得很有趣。Kotlin 要求你检查该值是否不为空，但这听起来并不容易。

### 检查一个 val 变量是否为空

```kotlin
val p1 = Person("Joanne", null, "Rowling")

if (p1.middle != null) {
    val middleNameLength = p1.middle.length
}
```

使用 if 语句检查 middle 属性是否不为空，如果是，Kotlin 将会进行智能转换：它将 p.middle 视为 String 类型而不是 String ？类型。这是可行的，但是因为变量 p 是用 val 关键字声明的，所以一旦设置便无法更改。如果使用另一种方式，即使用 var 而不是 val 声明。

```kotlin
var p1 = Person("Joanne", null, "Rowling")

if (p1.middle != null) {
    val middleNameLength = p1.middle!!.length
}
```

不能智能转换至String类型，因为p.middle是一个复杂的表达式。

非空断言（除非绝对必要，否则不要这么做）。

由于使用了 var 代替 val 来声明 p，所以 Kotlin 假定它可以在定义时与被访问 middle 属性时之间的时间里发生改变，并且不能使用智能转换。

解决此问题的一种方案是使用非空断言操作符 ( !! )。!!操作符将变量强制作为非空值看待，如果不正确，则引发异常。这是即使在 Kotlin 代码中仍然会遇到 NullPointerException 的少数几种情形之一，因此请尽量避免这种情况。

处理这种情况的更好方式是使用安全调用操作符 ( ?.)。安全的调用方法并在值为空时返回 null，参见示例。

### 使用安全调用操作符

```kotlin
val middleNameLength = p1.middle?.length
```

安全调用，结果类型是 Int?。

现在的问题在于结果类型仍然被推断为可空的，所以 middleNameLength 的类型是 Int?，但这可能不是你想要的。

### 安全调用与 Elvis 组合使用

```kotlin
val middleNameLength = p1.middle?.length?:0
```

当 middle 为空时，Elvis 操作符会返回 0。

Elvis 操作符会检查表达式左边的值，如果它不为空，则返回它。否则，该操作符会返回表达式右边的值。在这个案例中，它检查 p.middle?.length 的值是一个整数还是空。如果它是整数，该值会被返回。否则，该表达式返回 0。

Elvis 操作符的右侧可以是表达式，所以你可以在检查函数参数时使用 return 或 throw 语句。

最后，Kotlin提供了安全转换操作符as?。这样做的目的是避免在强制转换无法正常工作时引发 ClassCastException。例如，如果你尝试将一个可能为空的 Person 实例转型为 Person。

### 安全转换操作符

```kotlin
val p1 = p as ? Persion
```

变量 p1 的类型为 Person?。强制转换将成功，结果是一个 Person ；或者将失败，而 p1 的值将为 null。

## 打印不同的进制

为有效的进制使用扩展函数toString（radix：Int）。

```kotlin
println(42.toString(2))
```

```kotlin

```
