---
title: Kotlin学习(类与对象)
date: 2019-12-27 14:02:34
categories:
- Kotlin
tags:
- Kotlin
---

### 类和继承

一个类可以有一个主构造函数和多个次构造函数，主构造函数是类头的一部分

#### 主构造函数

```kotlin
class Person constructor(firstName: String) {/*...*/}
//如果主构造函数没有注解或任何可见的修饰符，constructor关键字可以省略
```

主构造函数不能有任何的代码，初始化的代码可以在初始化块中用`init`关键字标识

 实例初始化顺序按照init块出现顺序执行

<!-- more -->

主构造函数的参数可以在初始化块中使用，

```kotlin
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```

如果构造器有注解或者可见性修饰符，`constructor`关键字是必须的，修饰符在他之前

```kotlin
class Customer public @Inject constructor(name: String) {/*...*/}
```

#### 次构造函数

次构造函数以`constructor`开头

```kotlin
class Person {
    var children: MutableList<Person> = mutableListOf<Person>();
    constructor(parent: Person) {
        parent.children.add(this)
    }
}
```

如果类有主构造函数，每个次构造函数都要委托给主构造函数，使用`this`关键字

```kotlin
class Person(val name: String) {
    var children: MutableList<Personj> = mutableListOf<Person>();
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

初始化块会实际上变成主构造函数的一部分，委派给主构造函数在第一个次构造函数声明之前，执行也在之前，即使没有主构造函数，委派会隐式的发生并且执行。

#### 创建类实例

像调用普通方法一样调用构造函数，不需要`new`关键字

```kotlin
val invoice = Invoice()
val customer = Customer("JOe Smith")
```

**类成员**

* 构造函数和初始化块
* 函数
* 属性
* 嵌套类和内部类
* 对象声明

#### 继承

Kotlin中的所有类都有一个共同的超类`Any`, 对于没有超类声明的累，它是默认的超类

`Any`有三个方法`equals() hashCode() toString()`

显示声明一个超类，把类型放在冒号之后

```kotlin
open class Base(p: Int)
class Derived(p: Int) : Base(p)
```

如果派生类(derived class)没有主构造函数，那么每个次构造函数都要用`super`关键字初始化基类，或者委派另一个构造器初始化

#### 重写方法

如果一个方法要可重写，那么需要显示修饰符（open）

```kotlin
open class Shape {
    open fun draw() {/*...*/}
    //没有添加open，不能重写
    fun fill() {/*...*/}
}

class Circle() : Shape() {
    // override是必须的，否则编译不通过
    override fun draw() {/*...*/}
}
```

#### 重写属性

属性的重写必须被**初始化**

可以重写`val`的属性为`var`，反之不行

可以使用`override`关键字作为主构造函数属性声明的一部分

```kotlin
interface Shape {
    val vertexCount: Int
}

class Rectangle(override val vertexCount: Int = 4) : Shape
```

#### 调用父类实现

使用`super`关键字

内部类中使用`super@OuterClassName.`

#### 覆盖规则

```kotlin
open class Rectangle {
    open fun draw() { /* …… */ }
}

interface Polygon {
    fun draw() { /* …… */ } // 接口成员默认就是“open”的
}

class Square() : Rectangle(), Polygon {
    // 编译器要求覆盖 draw()：
    override fun draw() {
        super<Rectangle>.draw() // 调用 Rectangle.draw()
        super<Polygon>.draw() // 调用 Polygon.draw()
    }
}
```

#### 抽象类

类以及其中的某些成员可以声明为`abstract`，抽象成员在本类中可以不用实现，可以用一个抽象成员覆盖一个非抽象的开放成员

```kotlin
open class Polygon {
    open fun draw() {}
}

abstract class Rectangle : Polygon() {
    override abstract fun draw()
}
```

### 属性与字段

幕后字段（backing field）和幕后属性(backing property)

参见[该链接]( https://juejin.im/post/5b95321ae51d450e6475b7c6#heading-1 )

### 接口 Interface

```kotlin
interface MyInterface {
    fun bar()
    fun foo() {
        //可选实现
    }
}

class Child : MyInterface {
    override fun bar() {
        //body
    }
}
```

接口可以声明属性，属性可以是抽象或者提供访问器的实现，接口中的属性不能有幕后字段，因此接口中的访问器不能引用他们

```kotlin
interface MyInterface {
    val prop: Int // abstract
    
    val propertyWithImplementation: String
    	get() = "foo"
    
    fun foo() {
        print(prop)
    }
}

class Child : MyInterface {
    override val prop: Int = 29
}
```

#### 接口的继承

一个接口可以派生另一个接口，并有各自的方法和属性，类在实现接口时，只需定义缺少的实现

```kotlin
interface Named {
    val name: String
}

interface Person : Named {
    val firstName: String
    val lastName: Stirng
    
    override val name: String get() = "$firstName $lastName"
}

data class Employee(
	//name不需要实现，已经在Person中实现过了
    override val firstName: String, 
    override val lastName: String,
    val position: Position
) : Persion
```

### 可见性修饰符

类，对象，接口，构造函数，方法，属性以及他们的`setter`方法可以有可见性修饰符（`getter`和属性的修饰符一样），kotlin中有四种可见性修饰符`private, protected, internal, public`,默认为`public`

#### package中

* public在所有地方可见
* private只能在包含声明的文件内可见
* internal在同一个module内可见
* protected不能用在top-level中

#### 类和接口中

* private 在类中可见（包含所有它的元素）
* protected 和private一样，同时在子类中可见
* internal 能见到类声明的，本模块内的任何客户端都可见其internal成员
* public能见到类声明的任何客户端都可见其public成员

在Kotlin中，外部类不能访问内部类的private成员，如果重写一个protected成员并且没有显示指定其可见性，该成员还是protected可见性

```kotlin
open class Outer {
    private val a = 1
    protected open val b = 2
    internal val c = 3
    val d = 4  // public by default
    
    protected class Nested {
        public val e: Int = 5
    }
}

class Subclass : Outer() {
    // a is not visible
    // b, c and d are visible
    // Nested and e are visible

    override val b = 5   // 'b' is protected
}

class Unrelated(o: Outer) {
    // o.a, o.b are not visible
    // o.c and o.d are visible (same module)
    // Outer.Nested is not visible, and Nested::e is not visible either 
}
```

#### 构造方法

对主构造函数声明可见性, constructor关键字不能省略

```kotlin
class C private constructor(a: Int) {...}
```

局部声明（Local declarations)

局部变量，方法和类不能有可见性修饰符

### 扩展 Extensions

见[该链接]( https://juejin.im/post/5c74add5f265da2da15dc75b )

### 数据类 Data Classes

我们经常创建一些只保存数据的类，在这些类中，一些标准函数往往是从数据机械推导而来的，在kotlin中，这叫做数据类并标记为data：

```kotlin
data class User(val name: String, val age: Int)
```

* 主构造函数至少有一个参数
* 主构造函数的所有参数需要标记为val或var
* 数据类不能是抽象，开放，密封或者内部的
* 数据类只能实现**接口**（1.1之前）

在JVM中，如果生成的类需要含有一个无参的构造函数，则所有的属性必须指定默认值

#### 在类体中声明的属性

对于那些自动生成的函数，编译器只使用在主构造函数内部定义的属性，如需在生成的实现中排除一个属性，请将其声明在类体中。

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
```

#### 复制

```kotlin
val jack = User(name = "Jack", age=1)
val olderJack = jack.copy(age = 2)
```

###  泛型

[参考]( https://kaixue.io/kotlin-generics/ )

```kotlin
class Box<T>(t: T) {
    var value = t
}
//如果参数类型可以推断出来，可以省略类型参数
val box = Box(1)
```

#### 型变

Java有通配符类型，Kotlin有声明处型变（declaration-site variance)和类型投影（type projections）

要理解Kotlin泛型，先理解Java泛型

Java可以使用多态定义父类类型，比赋值子类类型，但是在泛型中没有这种多态关系

```java
TextView textView = new Button(context);
//这是多态
List<Button> buttons  = new ArrayList<Button>();
List<TextView> textViews = buttons;
//这里会报错，子类的泛型List<Button>不属于泛型List<TextView>的子类，textviews可以有多个子类
```

Java的泛型类型会在编译时进行类型擦除，数组没有编译时类型擦除

#### extends

`? extends`又称上届通配符，使Java泛型具备`协变性 Covariance`， 允许上面的多态实现

可以`get`数据，不能`add`数据，因为类型为未知`?`， get的一定使`TextView`的子类， 在add的时候，由于类型未知，所以可能是`List<Button>` 类型，这时候添加TextView不行

```java
List<? extends TextView> textViews = new ArrayList<Button>();
TextView textView = textView.get(0);//get可以实现
textViews.add(textView);//add报错，no suitable method found for add(TextView)
```

#### super

`? super` 叫做下届通配符，使Java具有`逆变性Contravariance`, super限制了？的子类型，称之为下届

```
List<? super Button> buttons = new ArrayList<TextView>();
Object object = buttons.get(0); // get 出来的是 Object 类型
Button button = ...
buttons.add(button); //  add 操作是可以的
```

小结，Java 的泛型本身是不支持协变和逆变的。

- 可以使用泛型通配符 `? extends` 来使泛型支持协变，但是**只能读取不能修改**，这里的修改仅指对泛型集合添加元素，如果是 `remove(int index)` 以及 `clear` 当然是可以的。
- 可以使用泛型通配符 `? super` 来使泛型支持逆变，但是**只能修改不能读取**，这里说的不能读取是指不能按照泛型类型读取，你如果按照 `Object` 读出来再强转当然也是可以的。

 这被称为 PECS 法则：**Producer-Extends, Consumer-Super**

#### Kotlin泛型 out in

```kotlin
class Producer<T> {
    fun product(): T {
        ...
    }
}

val producer: Producer<out TextView> = Producer<Button>()
val textView: TextView = producer.produce() //相当于List的 get
```

```kotlin
class Consumer<T> {
    fun consume(t: T) {
        ...
    }
}

val consumer: Consumer<in Button> = Consumer<TextView>()
consumer.consume(Button(context)) // 👈 相当于 'List' 的 'add'
```

 Kotlin 提供了另外一种写法：可以在声明类的时候，给泛型符号加上 `out` 关键字，表明泛型参数 `T` 只会用来输出，在使用的时候就不用额外加 `out` 了。 

```kotlin
class Producer<out T> {
    fun produce(): T {
        ...
    }
}
```

Java中，extends 边界设置多个使用`&`, Kotlin中边界使用`where` 

```java
class Monster<T extends Animal & Food>{
    
}
class Monster<T> where T: Animal, T: Food
```

### 嵌套类，内部类

```kotlin
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}
val demo = Outer.Nested().foo //==2
```

```kotlin
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
//内部类
```

### 枚举类

```kotlin
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
```

每个枚举常量是一个对象，用逗号隔开

#### 匿名类

每个枚举常量都可以声明他们自己的匿名类

```kotlin
enum class ProtocolState {
    WAITING {
        override fun signal() = TALKING
    },

    TALKING {
        override fun signal() = WAITING
    };

    abstract fun signal(): ProtocolState
}
```

#### 在枚举类中实现接口

```kotlin
enum class IntArithmetics : BinaryOperator<Int>, IntBinaryOperator {
    PLUS {
        override fun apply(t: Int, u: Int): Int = t + u
    },
    TIMES {
        override fun apply(t: Int, u: Int): Int = t* u
    };
    
    override fun applyAsInt(t: Int, u: Int) = apply(t, u)
}
```

### Object

对象表达式

创建一个匿名类继承自某种类型

```kotlin
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) {/*...*/}
    override fun mouseEntered(e: MouseEvent) {/*...*/}
})
```

对象声明

单例模式

```kotlin
object DAtaProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        //...
    }
    
    val allDataProviders: Collection<DataProvider>
    	get() = //...
}
```

在object关键字后跟一个名称，像变量声明一样，不是一个表达式，不能用在赋值语句的右边，可以有超类型

不能在局部作用域（即直接嵌套在函数内部），但是可以嵌套到其他对象声明或非内部类中。

**类内部的对象声明可以用companion关键字**

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
在 JVM 平台，如果使用 @JvmStatic 注解，你可以将伴生对象的成员生成为真正的静态方法和字段
```

对象表达式和对象声明之间的语义差异

对象表达式和对象声明之间有一个重要的语义差别：

* 对象表达式是在使用它们的地方立即执行（及初始化）的
* 对象声明是在第一次被访问到时延迟初始化
* 伴生对象的初始化是在相应的类被加载（解析）时，与Java静态初始化器的语义相匹配