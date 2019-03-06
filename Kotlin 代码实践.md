---
title: Kotlin 代码实践
categories: Kotlin
tags: Kotlin
---

## Hello world!
让 start() 方法返回字符串 “OK”  

<!-- more -->

答案  
```
fun start():String = "OK"
```

## 命名参数
函数 joinToString 使用参数的默认声明：
```
//String 扩展函数
fun joinToString(
    separator: String = ", ",
    prefix: String = "",
    postfix: String = "",
    /* ... */
): String
```
它可以在一系列字符串上调用。 仅指定两个参数使函数 joinOptions（）以JSON格式返回列表（例如，“[a，b，c]”）  

答案  
```
fun joinOptions(option:Collection<String>) = option.joinToString(prefix="[",postfix="]")
```

##默认参数
Java 中有几个 foo() 函数的重载：
```
public String foo(String name, int number, boolean toUpperCase) {
    return (toUpperCase ? name.toUpperCase() : name) + number;
}
public String foo(String name, int number) {
    return foo(name, number, false);
}
public String foo(String name, boolean toUpperCase) {
    return foo(name, 42, toUpperCase);
}
public String foo(String name) {
    return foo(name, 42);
}
```
所有这些Java重载都可以用Kotlin中的一个函数替换。 以使用foo编译的代码的方式更改函数foo的声明。 使用默认和命名参数。

答案
```
fun foo(name:String,number:Int = 42,toUpperCase:Boolean = false) = 
    if((toUpperCase)name.toUpperCase() else name)+number

fun useFoo() = listOf(
    foo("a"),
    foo("b",number = 1),
    foo("c",toUpperCase = true),
    foo(name = "d",number = 2,toUpperCase = true)
)
```

## Lambda表达式
将lambda传递给任何函数以检查集合是否包含偶数。 函数any将谓词作为参数获取，如果至少有一个元素满足谓词，则返回true。

答案：
```
fun containsEven(collection:Collection<Int>):Boolean = collection.any{
    it % 2 ==0
}
```

## 数据类
将以下Java代码重写为Kotlin：
```
public class Person {
    private final String name;
    private final int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

答案：
```
data class Person(val name:String,val age:Int)

fun getPeople():List<Person>{
    return listOf(Person("Alice",29),Person("Bob",31))
}
```

## 可空类型
阅读Kotlin中的null安全和安全调用，并仅使用一个if表达式重写以下Java代码：
```
public void sendMessageToClient(
    @Nullable Client client,
    @Nullable String message,
    @NotNull Mailer mailer
) {
    if (client == null || message == null) return;

    PersonalInfo personalInfo = client.getPersonalInfo();
    if (personalInfo == null) return;

    String email = personalInfo.getEmail();
    if (email == null) return;

    mailer.sendMessage(email, message);
}
```

答案：
```
fun sendMessageToClient(client:Client?,message:String?,mailer:Mailer){
    val email = client?.personalInfo?.email
    if(email!=null&&message!=null){
        mailer.sendMessage(email,message)
    }
}

class Client(val personalInfo:PersonalInfo?)
class PersonalInfo(val email:String?)
interface Mailer{
    fun sendMessage(email:String,message:String)
}
```

## Smart casts
使用智能转换和表达式重写以下Java代码：
```
public int eval(Expr expr) {
    if (expr instanceof Num) {
        return ((Num) expr).getValue();
    }
    if (expr instanceof Sum) {
        Sum sum = (Sum) expr;
        return eval(sum.getLeft()) + eval(sum.getRight());
    }
    throw new IllegalArgumentException("Unknown expression");
}
```

答案：
```
fun eval(expr):Int = 
    when(expr){
        is Num -> expr.value
        is Sum -> eval(expr.left)+eval(expr.right)
        else -> throw IllegalArgumentException("Unknown expression")
    }
```