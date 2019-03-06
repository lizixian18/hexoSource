---
title: Kotlin 类
categories: Kotlin
tags: Kotlin
---

## 类

在 Kotlin 中的一个类可以有一个主构造函数以及一个或多个次构造函数。  

<!-- more -->

### 主构造函数：
```
class Person(firstName: String) { ... }
```
 主构造函数不能包含任何的代码。初始化的代码可以放到以 init 关键字作为前缀的初始化块（initializer blocks）中。

 在实例初始化期间，初始化块按照它们出现在类体中的顺序执行，与属性初始化器交织在一起：
 ```
class InitOrderDemo(name: String) {
    val firstProperty = "First property: $name".also(::println)
    
    init {
        println("First initializer block that prints ${name}")
    }
    
    val secondProperty = "Second property: ${name.length}".also(::println)
    
    init {
        println("Second initializer block that prints ${name.length}")
    }
}
 ```

 ### 次构造函数

 类也可以声明前缀有 constructor 的次构造函数：
```
class Person {
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```

如果类有一个主构造函数，每个次构造函数需要委托给主构造函数， 可以直接委托或者通过别的次构造函数间接委托。委托到同一个类的另一个构造函数用 this 关键字即可：

```
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

请注意，初始化块中的代码实际上会成为主构造函数的一部分。委托给主构造函数会作为次构造函数的第一条语句，因此所有初始化块中的代码都会在次构造函数体之前执行。即使该类没有主构造函数，这种委托仍会隐式发生，并且仍会执行初始化块：

```
class Constructors {
    init {
        println("Init block")
    }

    constructor(i: Int) {
        println("Constructor")
    }
}
```

结果：  
Init block  
Constructor

## 创建类的实例
```
val invoice = Invoice()

val customer = Customer("Joe Smith")
```

注意 Kotlin 并没有 new 关键字。

## 嵌套类与内部类
类可以嵌套在其他类中：
```
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
```

## 内部类
类可以标记为 inner 以便能够访问外部类的成员。内部类会带有一个对外部类的对象的引用：
```
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
```

## 匿名内部类
使用对象表达式创建匿名内部类实例：
```
window.addMouseListener(object : MouseAdapter() {

    override fun mouseClicked(e: MouseEvent) { …… }

    override fun mouseEntered(e: MouseEvent) { …… }
})
```

## 继承
在 Kotlin 中所有类都有一个共同的超类 Any  
要声明一个显式的超类型，我们把类型放到类头的冒号之后：  
```
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

如果派生类有一个主构造函数，其基类型可以（并且必须） 用基类的主构造函数参数就地初始化。

如果类没有主构造函数，那么每个次构造函数必须使用 super 关键字初始化其基类型，或委托给另一个构造函数做到这一点。 注意，在这种情况下，不同的次构造函数可以调用基类型的不同的构造函数：
```
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```

## 覆盖方法
Kotlin 对于可覆盖的成员（我们称之为开放）以及覆盖后的成员需要显式修饰符：
```
open class Base {
    open fun v() { ... }
    fun nv() { ... }
}
class Derived() : Base() {
    override fun v() { ... }
}
```

Derived.v() 函数上必须加上 override 修饰符。如果没写，编译器将会报错。 如果函数没有标注 open 如 Base.nv()，那么子类中不允许定义相同签名的函数， 不论加不加 override。将 open 修饰符添加到 final 类（即没有 open 的类）的成员上不起作用。

标记为 override 的成员本身是开放的，也就是说，它可以在子类中覆盖。如果你想禁止再次覆盖，使用 final 关键字：
```
open class AnotherDerived() : Base() {
    final override fun v() { ... }
}
```

## 覆盖属性
属性覆盖与方法覆盖类似；在超类中声明然后在派生类中重新声明的属性必须以 override 开头，并且它们必须具有兼容的类型。每个声明的属性可以由具有初始化器的属性或者具有 getter 方法的属性覆盖。
```
open class Foo {
    open val x: Int get() { …… }
}

class Bar1 : Foo() {
    override val x: Int = ……
}
```

你也可以用一个 var 属性覆盖一个 val 属性，但反之则不行。这是允许的，因为一个 val 属性本质上声明了一个 getter 方法，而将其覆盖为 var 只是在子类中额外声明一个 setter 方法。

## 派生类初始化顺序
在构造派生类的新实例的过程中，第一步完成其基类的初始化（在之前只有对基类构造函数参数的求值），因此发生在派生类的初始化逻辑运行之前。
```
open class Base(val name: String) {

    init { println("Initializing Base") }

    open val size: Int = 
        name.length.also { println("Initializing size in Base: $it") }
}

class Derived(
    name: String,
    val lastName: String
) : Base(name.capitalize().also { println("Argument for Base: $it") }) { 

    init { println("Initializing Derived") }

    override val size: Int =
        (super.size + lastName.length).also { println("Initializing size in Derived: $it") }
}

结果：
Constructing Derived("hello", "world")
Argument for Base: Hello
Initializing Base
Initializing size in Base: 5
Initializing Derived
Initializing size in Derived: 10
```

这意味着，基类构造函数执行时，派生类中声明或覆盖的属性都还没有初始化。如果在基类初始化逻辑中（直接或通过另一个覆盖的 open 成员的实现间接）使用了任何一个这种属性，那么都可能导致不正确的行为或运行时故障。设计一个基类时，应该避免在构造函数、属性初始化器以及 init 块中使用 open 成员。

## 接口
