# [Kotlin 学习笔记](https://hltj.gitbooks.io/kotlin-reference-chinese/content/txt/basic-syntax.html)

## 基本语法

### 定义包

### 定义函数

* 可以将表达式作为函数体，返回值类型自动推断

### 定义变量

* 局部变量除外，val 变量自动 get 方法，var 变量自动生成 get、set 方法

### 注释

### 使用字符串模版

### 使用条件表达式

* 使用 if 作为表达式

### 使用可空值及 null 检测

* 当某个变量的值可以为 null 的时候，必须在声明处的类型后添加 ? 来标识该引用可为空

### 使用类型检测及自动类型转换

* is 运算符

### 使用 for 循环

* in 运算符 

### 使用 while 循环

### 使用 when 表达式

### 使用区间（range）

* 可以使用 in 运算符来检测某个数字是否在指定区间内

### 使用集合

* listOf() 和 setOf() 函数
* 使用 lambda 表达式来过滤（filter）和映射（map）集合

### 创建基本类及其实例

* 不需要 new 关键字


## 习惯用法

### 创建 DTOs（POJOs/POCOs）

* data class 会为 Customer 类提供以下功能：
	* 所有属性的 getters （对于 var 定义的还有 setters）
	* equals()
	* hashCode()
	* toString()
	* copy()
	* 所有属性的 component1()、 component2()……

### 函数的默认参数

### 过滤 list

### String 内插

### 类型判断
```
when (x) {
    is Foo //-> ……
    is Bar //-> ……
    else   //-> ……
}
```

### 遍历 map/pair型list
```
// k、v 可以改成任意名字
for ((k, v) in map) {
    println("$k -> $v")
}
```

### 使用区间
```
for (i in 1..100) { …… }  // 闭区间：包含 100
for (i in 1 until 100) { …… } // 半开区间：不包含 100
for (x in 2..10 step 2) { …… }
for (x in 10 downTo 1) { …… }
if (x in 1..10) { …… }
```

### 只读 list

### 只读 map
```
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```

### 访问 map
```
val map = mutableMapOf("a" to 1, "b" to 2, "c" to 3)
println(map["key"])
map["key"] = value
```

### 延迟属性
```
val p: String by lazy {
    // 计算该字符串
}
```

### 扩展函数
// 原文档这里的例子不大好，先跳过，后面有“扩展”的详细文档

### 创建单例
饿汉式：

```
object Singleton {
    fun test() {}
}
```

对应的 Java 实现：

```
public final class Singleton {
   public static final Singleton INSTANCE;

   public final void test() {
   }

   private Singleton() {
      INSTANCE = (Singleton)this;
   }

   static {
      new Singleton();
   }
}
```

懒汉式：

```
class LazySingleton private constructor(){
    companion object {
        val instance: LazySingleton by lazy { LazySingleton() }
    }
}
```
实现原理：

* 显示声明构造方法为 private
* 结合 companion object 和延迟属性创建实例
* lazy 默认是线程安全的，避免多线程同时产生多个实例

### If not null 缩写
```
val files = File("Test").listFiles()
println(files?.size)
```

### If not null and else 缩写
```
val files = File("Test").listFiles()
println(files?.size ?: "empty")
```

### if not null 执行代码
```
val value = ……

value?.let {
    …… // 代码会执行到此处, 假如data不为null
}
```

### 返回 when 表达式

### “try/catch”表达式

### “if”表达式

### 返回类型为 Unit 的方法的 Builder 风格用法
```
fun arrayOfMinusOnes(size: Int): IntArray {
    return IntArray(size).apply { fill(-1) }
}
```
apply 说明

```
/**
 * Calls the specified function [block] with `this` value as its receiver and returns `this` value.
 */
@kotlin.internal.InlineOnly
public inline fun <T> T.apply(block: T.() -> Unit): T { block(); return this }
```

### 单表达式函数

### 对一个对象实例调用多个方法 （with）
```
class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}

val myTurtle = Turtle()
with(myTurtle) { // 画一个 100 像素的正方形
    penDown()
    for(i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}
```

### Java 7 的 try with resources
```
val stream = Files.newInputStream(Paths.get("/some/file.txt"))
stream.buffered().reader().use { reader ->
    println(reader.readText())
}
```
use 说明

```
/**
 * Executes the given [block] function on this resource and then closes it down correctly whether an exception
 * is thrown or not.
 *
 * @param block a function to process this [Closeable] resource.
 * @return the result of [block] function invoked on this resource.
 */
@InlineOnly
public inline fun <T : Closeable?, R> T.use(block: (T) -> R): R {
    var closed = false
    try {
        return block(this)
    } catch (e: Exception) {
        closed = true
        try {
            this?.close()
        } catch (closeException: Exception) {
        }
        throw e
    } finally {
        if (!closed) {
            this?.close()
        }
    }
}
```

### 使用可空布尔
```
val b: Boolean? = ……
if (b == true) {
    ……
} else {
    // `b` 是 false 或者 null
}
```


## 编码规范

### Lambda 表达式
* lambda 表达应尽可能不要写在圆括号中
* 在非嵌套的短 lambda 表达式中，最好使用约定俗成的默认参数 it 来替代显式声明参数名 
* 在嵌套的有参数的 lambda 表达式中，参数应该总是显式声明

### 函数还是属性
底层算法优先使用属性而不是函数：

* 不会抛出任何异常
* O(1) 复杂度
* 计算廉价（或缓存第一次运行）
* 不同调用返回相同结果


## 基本类型

### 数字
* 数字字面值可以使用下划线
* 使用可空引用或泛型时会把数字装箱
* 数字装箱不必保留同一性(===),但保留了相等性(==)
* 较小的类型不能隐式转换为较大的类型，即需要显式转换

### 字符
* 字符用 Char 类型表示，不能直接当作数字

### 布尔
* 可空引用布尔会被装箱

### 数组
* Kotlin 中数组是不型变的（invariant）
* 有无装箱开销的专门的类来表示原生类型数组: ByteArray、 ShortArray、IntArray 等等

### 字符串
* 可用 for 循环迭代字符串
* 字符串字面值：转义字符串("")、原生字符串(""")
* 字符串可以包含模板表达式


## 包
* 如果出现名字冲突，可以使用 as 关键字在本地重命名冲突项来消歧义


## 控制流

### if 表达式

### When 表达式

### For 循环
* for 循环可以对任何提供迭代器（iterator）的对象进行遍历

### While 循环

## 返回与跳转

### 标签
* 在 Kotlin 中任何表达式都可以用标签（label）来标记，标签的格式为标识符后跟 @ 符号

### 标签处返回
* return 表达式从最直接包围它的函数中返回
* 需要从 lambda 表达式中返回，必须给它加标签并用以限制
* 通常情况下使用隐式标签更方便，该标签与接受该 lambda 的函数同名


## 类与继承

### 构造函数
* 一个主构造函数、多个次级构造函数，关键字 constructor
* 关键字 init
* 可以在主构造函数中声明属性

### 创建类实例
* 注意 Kotlin 并没有 new 关键字

### 继承
* 在 Kotlin 中所有类都有一个共同的超类 Any
* 关键字 open
* 关键字 override
* 调用超类实现 super、super@Outer
* 覆盖消除歧义可使用由尖括号中超类型名限定的 super，如 super<Base>
 
### 伴生对象


## 属性与字段

### 幕后字段
* field 标识符，只能用在属性的访问器内

### 幕后属性

### 编译期常量
* const 修饰符标记为编译期常量
* 位于顶层或者是 object 的一个成员
* 用 String 或原生类型 值初始化
* 没有自定义 getter

### 延迟初始化属性
* lateinit 修饰符


## 接口

### 接口中属性
*  接口中的属性不能有幕后字段

### 解决覆盖冲突


## 可见性修饰符
private、protected、internal 和 public


## 扩展

### 扩展函数

### 扩展属性
* 注意：由于扩展没有实际的将成员插入类中，因此对扩展属性来说幕后字段是无效的。这就是为什么扩展属性不能有初始化器。他们的行为只能由显式提供的 getters/setters 定义

### 可空接收者

### 伴生对象扩展

### 扩展声明为成员
* 声明为成员的扩展可以声明为 open 并在子类中覆盖。这意味着这些函数的分发对于分发接收者类型是虚拟的，但对于扩展接收者类型是静态的。


## 数据类

## 密封类
* sealed 修饰符

## 泛型

### 型变
* 声明处型变与类型投影

### 声明处型变
* 型变注解：out 和 in 

### 类型投影

### 星投影

### 泛型约束
* 上界

## 嵌套类

### 内部类
* inner

### 匿名内部类

## 枚举类
* enum


## 对象
* object

### 对象表达式
* 创建一个继承自某个（或某些）类型的匿名类的对象
* 需要“一个对象而已”，并不需要特殊超类型
* 对象表达式中的代码可以访问来自包含它的作用域的变量(不仅限于 final 变量)

### 对象声明
* 单例模式

### 伴生对象
* companion

### 对象表达式和对象声明之间的语义差异
* 对象表达式是在使用他们的地方立即执行（及初始化）的；
* 对象声明是在第一次被访问到时延迟初始化的；
* 伴生对象的初始化是在相应的类被加载（解析）时，与 Java 静态初始化器的语义相匹配。


## 委托

### 类委托
* 委托模式
* by

### 委托属性
* 例子

```
class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }

	// 对应 var 属性
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value has been assigned to '${property.name} in $thisRef.'")
    }
}
```

* 延迟属性 Lazy
	* lazy() 是接受一个 lambda 并返回一个 Lazy <T> 实例的函数，返回的实例可以作为实现延迟属性的委托
	* LazyThreadSafetyMode.SYNCHRONIZED（默认）、LazyThreadSafetyMode.NONE、LazyThreadSafetyMode.PUBLICATION

* 可观察属性 Observable
	* Delegates.observable()
	* Delegates.vetoable()

* 把属性储存在映射中

* 局部委托属性


## 函数

### 函数声明
* fun

### 默认参数

### 命名参数
* 通过使用星号操作符将可变数量参数（vararg） 以命名形式传入(请注意，在调用 Java 函数时不能使用命名参数语法，因为 Java 字节码并不总是保留函数参数的名称)

### 单表达式函数

### 可变数量的参数（Varargs）

### 中缀表示法

### 函数作用域

### 局部函数

### 尾递归函数
* 要符合 tailrec 修饰符的条件的话，函数必须将其自身调用作为它执行的最后一个操作。在递归调用后有更多代码时，不能使用尾递归，并且不能用在 try/catch/finally 块中


## 高阶函数
高阶函数是将函数用作参数或返回值的函数

* 下划线用于未使用的变量

* 带接收者的函数字面值

* 一个 lambda 表达式或匿名函数是一个“函数字面值”，即一个未声明的函数， 但立即做为表达式传递

### 内联函数

* inline
* noinline
* crossinline
* reified

## 协程
### 阻塞 VS 挂起
### 挂起函数
* suspend
* @RestrictsSuspension 注解
* yield


## 解构声明
* 下划线用于未使用的变量

## 集合
* 注意可变于不可变

## 区间
* .. 操作符
* in 和 !in
* downTo 函数
* step 函数
* until 函数
* reversed 函数

## 类型检查与转换
* is 与 !is
* as 与 as?

## This 表达式
* 限定的 this

## 相等性
* 引用相等(=== 和 !==) 
* 结构相等(== 和 !=)
* 注意：浮点数的相等性


## 操作符重载
* operator


## 空安全

## 异常
* try 是一个表达式
* Nothing 类型

## 注解


## 反射

### 类引用
* 要获得 Java 类引用， 请在 KClass 实例上使用 .java 属性

### 绑定的类引用

### 函数引用

* :: 操作符
* 当上下文中已知函数期望的类型时，:: 可以用于重载函数

```
fun isOdd(x: Int) = x % 2 != 0
fun isOdd(s: String) = s == "brillig" || s == "slithy" || s == "tove"

val numbers = listOf(1, 2, 3)
println(numbers.filter(::isOdd)) // 引用到 isOdd(x: Int)
```

### 属性引用

### 构造函数的引用

### 绑定的函数与属性引用


## 类型安全的构建器


## 类型别名













 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
