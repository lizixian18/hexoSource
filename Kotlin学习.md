---
title: Kotlin 基础
categories: Kotlin
tags: Kotlin
---

## 定义函数

带有两个 Int 参数、返回 Int 的函数：
```
fun sum(a Int,b:Int):Int{
    return a+b
}
```

<!-- more -->

将表达式作为函数体、返回值类型自动推断的函数：
```
fun sum(a:Int,b:Int) = a+b
```
函数默认参数：
```
fun foo(a: Int = 0, b: String = "") { …… }
```

## 定义变量

定义只读局部变量使用关键字 val 定义。只能为其赋值一次。
```
val a: Int = 1  // 立即赋值
val b = 2   // 自动推断出 `Int` 类型
val c: Int  // 如果没有初始值类型不能省略
c = 3       // 明确赋值
```

可重新赋值的变量使用 var 关键字：
```
var x = 5 // 自动推断出 `Int` 类型
x += 1
```

## 条件表达式
```
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}
```
使用 if 作为表达式:
```
fun maxOf(a:Int,b:Int) = if(a>b) a else b
```

## 使用可空值及 null 检测
某个变量的值可以为 null 的时候，必须在声明处的类型后添加 ? 来标识该引用可为空。  
如果 str 的内容不是数字返回 null：
```
fun parseInt(str:String):Int?{
    //...
}
```

## If not null 缩写
```
val files = File("Test").listFiles()

println(files?.size)
```

## If not null and else 缩写
```
val files = File("Test").listFiles()

println(files?.size ?: "empty")
```

## if null 执行一个语句
```
val values = ……
val email = values["email"] ?: throw IllegalStateException("Email is missing!")
```

## 在可能会空的集合中取第一元素
```
val emails = …… // 可能会是空集合
val mainEmail = emails.firstOrNull() ?: ""
```

## if not null 执行代码
```
val value = ……

value?.let {
    …… // 代码会执行到此处, 假如data不为null
}
```

## 映射可空值（如果非空的话
```
val value = ……

val mapped = value?.let { transformValue(it) } ?: defaultValueIfValueIsNull
```

## 使用类型检测及自动类型转换
is 运算符检测一个表达式是否某类型的一个实例。 如果一个不可变的局部变量或属性已经判断出为某类型，那么检测后的分支中可以直接当作该类型使用，无需显式转换：
```
fun getStringLength(obj: Any): Int? {
    if (obj is String) {
        // `obj` 在该条件分支内自动转换成 `String`
        return obj.length
    }

    // 在离开类型检测分支后，`obj` 仍然是 `Any` 类型
    return null
}
```

is 前面可以加感叹号即为相反意思 ：
```
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null
    // `obj` 在这一分支自动转换为 `String`
    return obj.length
}
```

## 循环
简单的创建一个列表：
```
val items = listOf("apple", "banana", "kiwifruit")
```
### for 循环：
```
val items = listOf("apple", "banana", "kiwifruit")
for (item in items) {
    println(item)
}
```
### while 循环
```
val items = listOf("apple", "banana", "kiwifruit")
var index = 0
while (index < items.size) {
    println("item at $index is ${items[index]}")
    index++
}
```
### when 表达式（取代了switch）
```
fun describe(obj: Any): String =
    when (obj) {
        1          -> "One"
        "Hello"    -> "Greeting"
        is Long    -> "Long"
        !is String -> "Not a string"
        in 1..10 -> print("x is in the range")
        x.isOdd() -> print("x is odd")
        else       -> "Unknown"
    }
```
### 使用区间（range）
使用 in 运算符来检测某个数字是否在指定区间内(区间外用 !in )：
```
val x = 10
val y = 9
if (x in 1..y+1) {
    println("fits in range")
}
```
区间迭代:
```
for (x in 1..5) {
    print(x)
}
```
数列迭代：
```
for (x in 1..10 step 2) {
    print(x)
}
println()
for (x in 9 downTo 0 step 3) {
    print(x)
}
```
输出：  
13579  
9630

```
for (i in 1..100) { …… }  // 闭区间：包含 100
for (i in 1 until 100) { …… } // 半开区间：不包含 100
for (x in 2..10 step 2) { …… }
for (x in 10 downTo 1) { …… }
if (x in 1..10) { …… }
```

### 使用 lambda 表达式来过滤（filter）与映射（map）集合：
```
val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
fruits
  .filter { it.startsWith("a") }
  .sortedBy { it }
  .map { it.toUpperCase() }
  .forEach { println(it) }
```
输出：  
APPLE  
AVOCADO

### 遍历 map/pair型list
```
for((k,v) in map){
    println("$k -> $v")
}
```

### 访问 map
```
println(map["key"])
map["key"] = value
```

## 延迟属性
```
val p:String by lazy{
    //计算该字符串
}
```

## 扩展函数
```
fun String.spaceToCamelCase() { …… }

"Convert this to camelcase".spaceToCamelCase()
```