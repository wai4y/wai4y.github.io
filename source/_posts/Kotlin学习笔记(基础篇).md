---
title: Kotlin学习笔记(基础篇)
date: 2019-12-17 15:33:46
categories:
- Kotlin
tags:
- Kotlin
---

### 注意事项

参考[官方文档]( https://kotlinlang.org/docs/reference/basic-syntax.html )

*  目录与包的结构无需匹配：源代码可以在文件系统的任意位置。

* 方法可以自动推断类型

*  没有三元运算，使用`if else`

* 可空`Int?`和非空检查

* 类型检查使用` obj is String`，这种情况可以不用显示转换

* `for`循环使用`item in items`, `when`类似于`switch`

* 区间：使用`in`来检测数字是否再某个区间`x in 1..y+1` (x是否在1和y+1 之间)

  * `!in`区间外
  * `x in 1..5`迭代
  * `x in 1..10 step 2` 或`x in 9 downTo 0 step 3`数列迭代

* lambda使用

  ```kotlin
  val fruits = listOf("banana", "avocado", "apple", "kiwifruit")
  fruits
    .filter { it.startsWith("a") }
    .sortedBy { it }
    .map { it.toUpperCase() }
    .forEach { println(it) }
  ```

* 创建基本类

  ```kotlin
  val rectangle = Rectangle(5.0, 2.0)
  val triangle = Triangle(3.0, 4.0, 5.0)
  ```

<!-- more -->

### 习惯用法

##### 创建DTOs（POJOs/POCOs)

```kotlin
data class Customer(val name: String, val email: String)
```

##### 方法默认值

```kotlin
fun foo(a: Int =0, b: String = " ") {...}
```

##### 过滤List

```kotlin
val positives = list.filter { x -> x>0 }
val positives = list.filter {it > 0}// 更简便写法
```

##### 只读的List 和Map

```kotlin
val list = listOf("a", "b", "c")
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
println(map["key"])
map["key"] = value
```

##### 非空和非空然后写法

```kotlin
val files = File("test").listFiles()
println(files?.size)
println(files?.size ?: "empty")// ?: 表示前面如果为空，则返回后面的"empty"
```

##### 如果非空，执行代码块

```kotlin
val value = ...
value?.let {
    ... //执行代码块
}
val mapped = value?.let { transformValue(it) } ?: defaultValue
//如果value或者转换结果为空，返回defaultValue
```

##### if when, try/catch可以写在val值后面

##### 单表达式函数一般与其他表达式结合

```kotlin
fun theAnswer() = 42
fun theAnswer(color: String) Int = when (color){
    "Red" -> 0
    "Green" -> 1
    "Blue" -> 2
    else -> throw IllegalArgumentExption("Invalid color param value")
}
```

##### 对一个对象实例调用多个方法使用with

```kotlin
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}

val myTurtle = Turtle()
with(myTurtle) { //draw a 100 pix square
    penDown()
    for(i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}
```

##### 设置对象的属性使用 apply

```kotlin
val myRectangle = Rectangle().apply {
    length = 4
    breadth = 5
    color = 0xFAFAFA
}
//对于不在构造函数中的属性很有用
```

##### 可空布尔值

```kotlin
val b: Boolean? = ...
if (b == true) {
    ...
} else {
    // b为false 或 null
}
```

##### 交换两个变量

```kotlin
var a = 1
var b = 2
a = b.also { b = a}
```

##### 使用TODO标记

```kotlin
fun calcTaxes(): BigDecimal = TODO("Waiting for feedback from accounting")
```

PS：

* 用于创建实例的工厂类可以与要创建的类名称相同

* 测试方法的名称可以使用反括号括起来， Android不支持

* 常量或者顶层对象 val属性，使用大写，下划线分割名称

* 保存单例对象引用的属性的名称可以使用与`object`声明相同的命令风格

  ```kotlin
  val PersonComparator: Comparator<Person> = /*...*/
  ```




### 基本类型

在kotlin中，一切皆对象，我们可以在任何变量上调用成员方法和属性，一些类型有特殊的内部表示，`numbers, characters, booleans` 在运行时能当作基础类型的值， 但是在用户开来和传统的类无异。

基本类型包括： numbers, characters, booleans, arrays, strings

##### Numbers

![numbers](/Kotlin学习笔记(基础篇)/numbers.png)

| Type   | Size(bits) | Significant bits | Exponent bits | Decimal digits |
| ------ | ---------- | ---------------- | ------------- | -------------- |
| Float  | 32         | 24               | 8             | 6-7            |
| Double | 64         | 53               | 11            | 15-16          |

整型默认推断为Int， 浮点型默认推断为Double类型

```kotlin
val one = 1 // Int
val threeBillion = 3000000000 //Long
val oneLone = 1L
var pi = 3.14 // Double
var efloat = 2.33f // Float
//可以在Kotlin中使用下划线表示数字字面值
val oneMillion = 1_000_000
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

Kotlin中没有隐式的类型转换，如果方法参数为`Double`，必须传入`Double`类型

在java平台，numbers作为JVM基本类型进行物理存储，除非我们需要可空的number引用`Int?` ， 或者有泛型

number的装箱不一定保持一致性(identity ===)，但是保持了相等性（equal()）

可以进行显示的类型转换

```kotlin
val i: Int = b.toInt()
```

##### Characters

characters 可以展示为Char类型，不能直接作为numbers来对待

character 使用单引号表示`'1'`, 特殊字符使用反斜杠`\t, \n, \b`来表示，编码其他字符使用转义字符语法`\uFF00`

##### Booleans

布尔在可空引用时会被装箱

##### Arrays

Array在kotlin中展示为Array 类， 有get和set方法，以及size属性，还有一些其他的成员方法

```kotlin
class Array<T> private constructor() {
    val size: Int
    operator fun get(indexx: Int): T
    operator fun set(index: Int, value: T): Unit
    
    operator fun iterator(): Iterator<T>
}
```

创建一个数组， 使用`arrayOf(1， 2， 3)` ，这样就创建了一个`[1, 2, 3]`的数组, `arrayOfNulls()`能创建一个指定size的null array

#####  String

字符串的元素可以使用索引来方法`str[i]`，使用for循环来遍历

字符串字面量：

**转义字符串（escaped string）**可以有转义字符

```kotlin
val s = "Hello, world!\n"
```

**原始字符串（a raw string）** 使用三个引号`"""`来表示，其中可以包含换行和其他字符

```kotlin
val text = """
	for (c in "foo")
		print(c)
"""
//可以使用trimMargin()来去除前面的空格， 默认是|做边界前缀，不过也可以用其他字符来作为参数传入，比如
// trimMargin(">")
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()

```

**字符串模板**使用`$`符号表示

```kotlin
val i = 10
println("i = $i") // prints "i = 10"

val s = "abc"
println("$s.length is ${s.length}") // prints "abc.length is 3"

val price = """
${'$'}9.99
"""
```

### 控制流（if, when, for, while）

* if 可以作为块，最后的表达式是这个块的值

```kotlin
val max = if (a > b) {
    print("Choose a")
    a
} else {
    print("Choose b")
    b
}
```

* when类似于switch语句，可以作为表达式（expression）或者语句（statement）， 表达式一定有值，语句不一定有值，作为表达式时，else分支是强制的

```kotlin
when (x) {
    1 -> print("x==1")
    2 -> print("x==2")
    else -> {//note the block
        print("x is neither 1 nor 2")
    }
}
//如果多种情况的结果是一样的，那么分支条件可以用逗号分隔
when (x) {
    0, 1 -> print("x ==0 or x==1")
    else -> print("otherwise")
}
//可以使用任意（arbitrary）表达式作为分支的条件
when (x) {
    parseInt(s) -> print("s encodes x")
    else -> print("s does not encode x")
}
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
//可以使用is 或者 !is来检测类型，由于只能转换，可以直接使用类型的方法，不需要额外的检查
fun hasPrefix(x: Any) = when(x) {
    is String -> x.startWith("prefix")
    else -> false
}
//从Kotlin 1.3开始， 可以将when的主语捕获到变量中
fun Request.getBody() =
		when (val response = executeRequest()){
            is Success -> response.body
            is HttpError -> throw HttpException(response.status)
        }
```

* for 循环可以对任何提供迭代器（iterator）的对象进行遍历，类似foreach

```kotlin
for (i in 1..3) {
    println(i)
}
for (i in 6 downTo 0 step 2) {
    println(i)
}
//通过索引遍历
for (i in array.indices) {
    println(array[i])
}
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
```



