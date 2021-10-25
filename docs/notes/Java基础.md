# Java基础

## 数据类型

### 基本数据类型

| 数据类型 | 存储字节 | 范围                                                         |
| -------- | -------- | ------------------------------------------------------------ |
| byte     | 1        | -128~127                                                     |
| short    | 2        | -32768~32767                                                 |
| int      | 4        | -2147483648~2147483647（21亿多）                             |
| long     | 8        | -9223372036854775808~9223372036854775807（19位数）           |
| float    | 4        | -3.4E+38F ~ 3.4E+38F（6~7位有效数字）                        |
| double   | 8        | -1.7E-308～1.7E+308（15~16位有效数字）                       |
| bool     | ~        | true/false（可以用1个bit位实现，但JVM一般用int来存储，数组用byte数组实现） |
| char     | 2        | UTF-16字符编码（1个Unicode编码点的字符，可能需要4个字节UTF-16编码单元来表示，1个char装不下） |



### 引用数据类型

引用数据类型包括：

* 类
* 接口
* 数组

基本类型都有对应的包装类型，基本类型与其对应的包装类型之间的赋值使用自动装箱与拆箱完成。

```Java
Integer x = 2;     // 装箱 调用了 Integer.valueOf(2)
int y = x;         // 拆箱 调用了 X.intValue()
```

基本类型的精度不能要求时，可以使用java.math包中的BigInteger和BigDecimal类。

### 缓存池

new Integer(123) 与 Integer.valueOf(123) 的区别在于：

- new Integer(123) 每次都会新建一个对象；
- Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。

```Java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);    // false
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k);   // true
```

valueOf() 方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。

基本类型对应的缓冲池如下（可调整）：

- boolean:  true/false
- byte,short,int: -128 ~ 127
- char: \u0000 ~ \u007F(ASCII)



## 运算

* 操作符优先级从高到低列出：

![image-20210818224802316](pics/Java%E5%9F%BA%E7%A1%80/image-20210818224802316.png)

* java.lang包中的Math类提供了许多数学运算方法。比如：Math.sin(double)、Math.max(int,int)等。

* Java 不能隐式执行向下转型

  ~~~java
  short i = (short)1; 	// 整数字面量的类型是int
  float f = (float)3.14; 	// 数字字面量的类型是double,float的字面量是：3.14F
  float f = 21_1234_1234; // int或long可以隐式转型为float或double但可能丢失信息。
  ~~~

  

## 字符串

### 不可变性

String具有不可变性，其本身被声明为final不可以被继承，Java 8 中使用final的char数组存储数据，初始化后无法改变。

String的排序根据Unicode值来判断。compareTo方法返回”this.charAt(k)-anotherString.charAt(k)“‘this.length()-anotherString.length()“。

### 字符串常量池

 字符串常量池（String Pool）保存着所有字符串字面量（literal strings），这些字面量在编译时期就确定。不仅如此，还可以使用 String 的 intern() 方法在运行过程将字符串添加到 String Pool 中。

当一个字符串调用 intern() 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 equals() 方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool 中添加一个**引用指向该字符串**（Java 8），并返回。

```Java
String s1 = new String("aaa");			
String s2 = new String("aaa");			
System.out.println(s1 == s2);           // false
String s3 = s1.intern();
String s4 = s2.intern();
System.out.println(s3 == s4);           // true
```

![image-20210819082914255](pics/Java%E5%9F%BA%E7%A1%80/image-20210819082914255.png)

### StringBuffer,StringBuilder

**1. 可变性**

- String 不可变
- StringBuffer 和 StringBuilder 可变

**2. 线程安全**

- String 不可变，因此是线程安全的
- StringBuilder 不是线程安全的
- StringBuffer 是线程安全的，内部使用 synchronized 进行同步

## 面向对象

### 概述

​	类：类是一种**抽象**，一个类别，一个模板，它描述一类对象的行为和状态，是一组具有相同特性（属性）与行为（方法）的事物集合。

​	对象：是一个个性的产物，是一个个体的特征，是类的一个**实例**，有状态和行为。

​	面向对象的三大特征是封装、继承和多态。

​	封装：设计良好的模块会隐藏所有的实现细节，把它的 API 与它的实现清晰地隔离开来。访问权限应当尽可能地使每个类或者成员不被外界访问。

​	继承：继承就是子类继承父类的特征和行为，子类拥有父类非 private 的属性、方法。

​	多态：多态是同一个行为具有多个不同表现形式或形态的能力。子类可以重写继承的方法。

### static

1. **静态变量**

   又称为类变量，也就是说这个变量属于类的，类所有的实例都共享静态变量，可以直接通过类名来访问它。

2. **静态方法**

   静态方法在类加载的时候就存在了，它不依赖于任何实例。所以静态方法必须有实现，也就是说它不能是抽象方法。

   不能直接访问所属类的实例字段和实例方法，方法中不能有 this 和 super 关键字，因为这两个关键字与具体对象关联。

3. **静态语句块**

   静态语句块在类初始化时运行一次。

4. **静态内部类**

   静态内部类相当于一个普通类，不能直接访问外部类的非静态的变量和方法，但相互拥有私有访问权限。可以用来实现单例模式。

   ~~~java
   public class Singleton {  
       private static class SingletonHolder {  
       private static final Singleton INSTANCE = new Singleton();  
       }  
       private Singleton (){}  
       public static final Singleton getInstance() {  
       return SingletonHolder.INSTANCE;  
       }  
   }
   ~~~

5. **静态导包**

   在使用静态变量和方法时不用再指明 ClassName，从而简化代码，但可读性大大降低。

### 内部类

​	内部类就是在一个类的内部定义的类。分为静态内部类、成员内部类、局部内部类和匿名内部类。

​	静态内部类的加载和外部类加载无关；静态内部类相当于一个普通类，不能直接访问外部类的非静态的变量和方法。

​	成员内部类（非静态内部类）依赖于外部类的实例：需要先创建外部类实例，才能用这个实例去创建成员内部类；成员内部类可以直接访问外部类所有变量，方法（外部类.this.）；成员内部类不能有静态的东西。

​	局部内部类的定义在方法内，作用域只在方法中。局部内部类使用的局部参数，编译器默认会为这个变量加上final。

~~~java
public class OuterClass {
    private String field;

    class InnerClass{
        private String field;
        public void func(){
            OuterClass.this.field = "abc";
        }
    }
    static class  StaticInnerClass{
        private String field;
        public void func(){
            // OuterClass.this.field = "abc"; // error
        }
    }

    public static void main(String[] args) {        
        StaticInnerClass staticInnerClass = new OuterClass.StaticInnerClass();
        
        // InnerClass innerClass = new OuterClass.InnerClass(); // error
        OuterClass outerClass = new OuterClass();
        InnerClass innerClass = outerClass.new InnerClass();
        innerClass.func();
        System.out.println(outerClass.field);   // abc

        class LocalClass{
            
        }
        LocalClass localClass = new LocalClass();
    }
}
~~~

​	匿名内部类相当于没有名字的局部内部类，继承或实现了其他类，并且不能有构造方法。

~~~java
public class AnonClass {
    public static void main(String[] args) {
        driveCar(new Car() {
            @Override
            public void drive() {
                
            }
        });
    }
    public static void driveCar(Car car){
        car.drive();
    }
}

interface Car {
    void drive();
}
~~~





###  初始化顺序

​	静态变量和静态语句块在类加载时进行，优先于实例变量和普通语句块。变量和语句块的初始化顺序取决于它们在代码中的顺序。（子类的构造函数的第一条语句必须是父类构造函数，无参构造可以隐式调用，省略不写。）

- 父类（静态变量、静态语句块）
- 子类（静态变量、静态语句块）
- 父类（实例变量、普通语句块）
- 父类（构造函数）
- 子类（实例变量、普通语句块）
- 子类（构造函数）

### 访问权限

Java 中有三个访问权限修饰符：private、protected 以及 public，如果不加访问修饰符，表示包级可见。

可以对类或类中的成员（字段和方法）加上访问修饰符。

- 类可见表示其它类可以用这个类创建实例对象。
- 成员可见表示其它类可以用这个类的实例对象访问到该成员；

protected 用于修饰成员，表示在继承体系中成员对于子类可见，但是这个访问修饰符对于类没有意义。

### 重写与重载

**1. 重写（Override）**

存在于继承体系中，指子类实现了一个与父类在方法声明上完全相同的一个方法。

为了满足里式替换原则（**子类对象可以在程序中代替其父类对象**），重写有以下三个限制：

- 子类方法的访问权限必须大于等于父类方法；
- 子类方法的返回类型必须是父类方法返回类型或为其子类型。
- 子类方法抛出的异常类型必须是父类抛出异常类型或为其子类型。

使用 @Override 注解，可以让编译器帮忙检查是否满足上面的三个限制条件。

方法调用的优先级为：

- this.func(this)
- super.func(this)
- this.func(super)
- super.func(super)

```java
/*
    A
    |
    B
    |
    C
    |
    D
 */


class A {

    public void show(A obj) {
        System.out.println("A.show(A)");
    }

    public void show(C obj) {
        System.out.println("A.show(C)");
    }
}

class B extends A {

    @Override
    public void show(A obj) {
        System.out.println("B.show(A)");
    }
}

class C extends B {
}

class D extends C {
}
public static void main(String[] args) {

    A a = new A();
    B b = new B();
    C c = new C();
    D d = new D();

    // 在 A 中存在 show(A obj)，直接调用
    a.show(a); // A.show(A)
    // 在 A 中不存在 show(B obj)，将 B 转型成其父类 A
    a.show(b); // A.show(A)
    // 在 B 中存在从 A 继承来的 show(C obj)，直接调用
    b.show(c); // A.show(C)
    // 在 B 中不存在 show(D obj)，但是存在从 A 继承来的 show(C obj)，将 D 转型成其父类 C
    b.show(d); // A.show(C)

    // 引用的还是 B 对象，所以 ba 和 b 的调用结果一样
    A ba = new B();
    ba.show(c); // A.show(C)
    ba.show(d); // A.show(C)
}
```

**2. 重载（Overload）**

存在于同一个类中，指一个方法与已经存在的方法名称上相同，但是参数类型、个数、顺序至少有一个不同。

应该注意的是，返回值不同，其它都相同不算是重载。

```
class OverloadingExample {
    public void show(int x) {
        System.out.println(x);
    }

    public void show(int x, String y) {
        System.out.println(x + " " + y);
    }
}
public static void main(String[] args) {
    OverloadingExample example = new OverloadingExample();
    example.show(1);
    example.show(1, "2");
}
```

可变参数必须位于最后一项，Java当做数组处理。当出现固定参数与可变参数方法重载时（极不推荐使用）：

- 在调用方法的时候，如果能够和固定参数的方法匹配，也能够与可变长参数的方法匹配，则选择固定参数的方法
- 如果要调用的方法可以和两个可变参数匹配，则出现错误

~~~java
public class VarArgsTest {
public void print(String... args) {
        for (int i = 0; i < args.length; i++) {
            System.out.println(args[i]);
        }
    }
public void print(String test,String...args ){
          System.out.println("----------");
    }
public static void main(String[] args) {
        VarArgsTest test = new VarArgsTest();
        test.print();  					// 调用print(String... args)
        // test.print("hello");  			// 编译错误
        // test.print("hello", "alexia");	// 编译错误
    }
}
~~~

### final与abstract

#### final

1. **数据** 

   声明属性为常量，可以是编译时常量，也可以是在运行时被初始化后不能被改变的常量（对象初始化完成前必须赋值）。

   * 对于基本类型，final 使数值不变；
   * 对于引用类型，final 使引用不变，也就不能引用其它对象，但是被引用的对象本身是可以修改的。

2. **方法**

   声明方法不能被子类重写。

   private 方法隐式地被指定为 final，如果在子类中定义的方法和基类中的一个private 方法签名相同，此时子类的方法不是重写基类方法，而是在子类中定义了一个新的方法。

3. **类**

   声明类不允许被继承。

#### abstract

抽象类和抽象方法都使用 abstract 关键字进行声明。如果一个类中包含抽象方法，那么这个类必须声明为抽象类。

抽象类和普通类最大的区别是，抽象类不能被实例化，只能被继承。



## 接口与lambda表达式

### 概述

接口是抽象类的延伸，从 Java 8 开始，接口也可以拥有默认的方法实现（需要default修饰）。接口的成员（字段 + 方法）默认都是 public 的，字段默认static 和 final 的，可以拥有静态方法（必须实现，常用作工厂方法）。

如果实现的多个接口方法有冲突，并且其中存在方法的实现，子类必须显式解决冲突（实现方法或抽象方法）。**类优先于接口**，当接口与父类冲突时，忽略接口方法。

### 与抽象类比较

- 从设计层面上看，抽象类提供了一种 IS-A 关系，需要满足里式替换原则，即子类对象必须能够替换掉所有父类对象。而接口更像是一种 LIKE-A 关系，它只是提供一种方法实现契约，并不要求接口和实现接口的类具有 IS-A 关系。
- 从使用上来看，一个类可以实现多个接口，但是不能继承多个抽象类。
- 接口的字段只能是 static 和 final 类型的，而抽象类的字段没有这种限制。
- 接口的成员只能是 public 的，而抽象类的成员可以有多种访问权限。

### lambda表达式

1. Lambda 允许把函数作为一个方法的参数，实现**延迟执行**该函数。Java的实现方式是把有且只有一个抽象方法的接口作为方法参数，这种接口被称作函数式接口，可以使用@FunctionalInterface注解，要求编译器检查合规性。

   ~~~java
   // 方法定义为public static <T> void sort(T[] a, Comparator<? super T> c)，调用使用lambda表达式实现Comparator接口
   Arrays.sort(words,(String first,String second)->{int rst = first.length()-second.length();return rst;});
   // 当编译器可以推断出参数类型时，类型可以省略。单条语句可以省略{}和return
   Arrays.sort(words,(first,second)->{first.length()-second.length()});
   ~~~

2. 可以引用已经有实现的方法作为λ表达式。并使用更加简洁的方式来表达。

   * 类::静态方法

   * 对象::实例方法

   * 类::实例方法（函数的第一个参数为调用对象，后续参数为实例方法参数）

     ~~~JAVA
     Arrays.sort(words,String::compareToIgnoreCase) // 相当于 (x,y)->x.compareToIgnoreCase(y)
     ~~~

   * 类::new（构造函数）

3. lambda表达式相当于匿名内部类，其使用的局部参数，编译器默认会为这个变量加上final。

4. 一般函数类型都是结构化的，可以使用Java 内置的函数式接口来达到声明函数的目的。比如四大核心函数式接口：

   | 函数式接口       | 参数类型 | 返回类型 | 抽象方法名 | 用途                         |
   | ---------------- | -------- | -------- | ---------- | ---------------------------- |
   | Supplier\<T\>    | none     | T        | accept     | 生产者                       |
   | Consumer\<T\>    | T        | void     | accept     | 消费者                       |
   | Functiong\<T,R\> | T        | R        | apply      | 操作T类型对象，返回R类型结果 |
   | Predicate\<T\>   | T        | boolean  | test       | 断言                         |

5. 处理和返回函数的函数被称为高阶函数，比如Comparator中的许多静态方法：

   ~~~java
   Arrays.sort(people,Comparator.comparing(Person::getName));
   Arrays.sort(people,Comparator.comparing(Person::getLastName).thenComparing(Person::getFirstName)) // thenComparing连接
   ~~~

   

## Object

### 概述

| 方法                            | 说明                                                        |
| ------------------------------- | ----------------------------------------------------------- |
| String toString()               | 默认返回该对象类名和哈希码，比如： ToStringExample@4554617c |
| boolean equals(Object other)    | 默认判断是否同一对象。                                      |
| int hashCode()                  | 等价的对象hashCode必须相同。默认hashCode由虚拟机分配        |
| Class<?> getClass()             | 返回Class对象                                               |
| protected native Object clone() | 保护方法，默认浅拷贝                                        |
| protected void finalize()       | 当对象回收时调用1次该方法。                                 |
| wait、notify、notifyAll         | 多线程相关                                                  |

### equals

**1. 等价与相等**

* 对于基本类型，== 判断两个值是否相等，基本类型没有 equals() 方法。

- 对于引用类型，== 判断两个变量是否引用同一个对象，而 equals() 判断引用的对象是否等价。

**2. 实现**

- 检查是否为同一个对象的引用，如果是直接返回 true；
- 检查是否是同一个类型，如果不是，直接返回 false；
- 将 Object 对象进行转型；
- 判断每个关键域是否相等。

**3. hashCode**

​	等价的对象hashCode必须相同。理想的哈希函数应当具有均匀性。即不相等的对象应当均匀分布到所有可能的哈希值上。这就要求了哈希函数要把所有域的值都考虑进来。可以将每个域都当成 R 进制的某一位，然后组成一个 R 进制的整数。

R 一般取 31，因为它是一个不大不小的奇素数，如果是偶数的话，因为与 2 相乘相当于向左移一位，最左边的位丢失增加了碰撞几率。并且一个数与 31 相乘编译器会自动进行优化：31*x  ==> (x<<5)-x。

```java
@Override
public int hashCode() {
    int result = 17;
    result = 31 * result + x;
    result = 31 * result + y;
    result = 31 * result + z;
    return result;
}
```

### clone

**1.cloneable**

​	clone() 是 Object 的 protected 方法，它不是 public，一个类不显式去重写 clone()，其它类就不能直接去调用该类实例的 clone() 方法。重写clone()需要实现Cloneable 接口，否则将抛出CloneNotSupportedException

**2.浅拷贝与深拷贝**

​	Object默认的浅拷贝，拷贝对象和原始对象的引用类型属性引用的是同一个对象。重写clone()方法需要进行变更。

~~~java
public class DeepCloneExample implements Cloneable {

    private int[] arr;

    public DeepCloneExample() {
        arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
    }

    public void set(int index, int value) {
        arr[index] = value;
    }

    public int get(int index) {
        return arr[index];
    }

    @Override
    protected DeepCloneExample clone() throws CloneNotSupportedException {
        DeepCloneExample result = (DeepCloneExample) super.clone();
        // 深拷贝变更引用
        result.arr = new int[arr.length];
        for (int i = 0; i < arr.length; i++) {
            result.arr[i] = arr[i];
        }
        return result;
    }
}
~~~

**3.替代方案**

使用 clone() 方法来拷贝一个对象即复杂又有风险，它会抛出异常，并且还需要类型转换。Effective Java 书上讲到，最好不要去使用 clone()，可以使用拷贝构造函数或者拷贝工厂来拷贝一个对象。

```java
public class CloneConstructorExample {

    private int[] arr;

    public CloneConstructorExample() {
        arr = new int[10];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = i;
        }
    }

    public CloneConstructorExample(CloneConstructorExample original) {
        arr = new int[original.arr.length];
        for (int i = 0; i < original.arr.length; i++) {
            arr[i] = original.arr[i];
        }
    }

    public void set(int index, int value) {
        arr[index] = value;
    }

    public int get(int index) {
        return arr[index];
    }
}
```



## 枚举

* 枚举是特殊的类，继承自Enum\<E\>类。 相当于一个有固定实例集的类，每个实例拥有final的序号数（int)和实例名(String)，序号数从0开始。
* velues方法返回该枚举按序号数排序的数组。枚举的equals,hashCode和compareTo方法为final，compareTo方法按照序号数排序。
* toString和valueOf方法默认实现实例名与枚举实例直接转换（可重写）。
* 枚举天生单例，构造函数总是private的。静态属性初始化和静态初始化块在构造结束后执行。
* 可以给单个实例单独覆盖枚举中已经定义的方法。

~~~java

public enum Size {
    SMALL("S"){
        @Override
        public double getPricingFactor() {
            return 0.8;
        }
    },
    MEDIUM("M"),
    LARGE("L"){
        @Override
        public double getPricingFactor() {
            return 1.2;
        }
    };
    private String abbreviation;
    Size(String abbreviation) {
        this.abbreviation = abbreviation;
    }
    public String getAbbreviation(){
        return this.abbreviation;
    }
    public double getPricingFactor(){
        return 1.0;
    }
}
~~~



## 反射、泛型与注解（动态性扩展）

### 反射概述

动态语言，是指程序在运行时可以改变其结构：新的函数可以引进，已有的函数可以被删除等结 构上的变化。比如常见的JavaScript 就是动态语言，除此之外 Ruby,Python 等也属于动态语言， 而 C、C++则不属于动态语言。从反射角度说JAVA 属于半动态语言。

在 Java 中的反射机制是指在运行状态中，对于任意一个类都能够知道这个类所有的属性和方法； 并且对于任意一个对象，都能够调用它的任意一个方法；这种动态获取信息以及动态调用对象方法的功能成为Java 语言的反射机制。

![Java 反射](pics/Java%E5%9F%BA%E7%A1%80/Java%E5%8F%8D%E5%B0%84.png)

### 反射的应用场合

在 Java 程序中许多对象在运行是都会出现两种类型：编译时类型和运行时类型。 编译时的类型由声明对象时实用的类型来决定，运行时的类型由实际赋值给对象的类型决定 。

~~~java
Person p=new Student();	// p的编译时类型为Person，运行时类型为 Student
~~~

程序在运行时还可能接收到外部传入的对象，该对象的编译时类型为 Object,但是程序有需要调用该对象的运行时类型的方法。为了解决这些问题，程序需要在运行时发现对象和类的真实信息。然而，如果编译时根本无法预知该对象和类属于哪些类，程序只能依靠运行时信息来发现该对象和类的真实信息，此时就必须使用到反射了。

### Java 反射API

反射API用来生成JVM中的类、接口或则对象的信息。

1. Class 类：反射的核心类，可以获取类的属性，方法等信息。

2. Field 类：java.lang.reflect 包中的类，表示类的成员变量，可以用来获取和设置类之中的属性值。

3. Method 类： java.lang.reflect 包中的类，表示类的方法，它可以用来获取类中的方法信息或者执行方法。

4. Constructor 类： java.lang.reflect 包中的类，表示类的构造方法。
5. Parameter类：java.lang.reflect 包中的类，表示方法的参数。

### 反射使用步骤（获取 Class 对象、调用对象方法）

1. 获取想要操作的类的 Class 对象，他是反射的核心。

2. 调用Class 类中的方法，既就是反射的使用阶段。

3. 使用反射API 来操作这些信息。

### 获取 Class 对象

1. 通过具体的类或对象获取

   ~~~java
   Person p=new Person(); 
   Class clazz=p.getClass();
   Class clazz=Person.class;
   ~~~

2. 使用加载器加载

   * 使用 Class 类中的 forName静态方法(最安全/性能最好)

     ~~~JAVA
     // Class.forName(className, true, currentLoader)的重载方法：执行静态初始化，使用当前加载器
     Class clazz=Class.forName(“com.mysql.jdbc.Driver”);
     ~~~

   * 加载器的loadClass方法，极不推荐，不会执行静态初始化。

     ~~~java
     // Driver类中有必须的静态初始化，此加载无意义。 
     Class clazz=this.getClass().getClassLoader().loadClass("com.mysql.jdbc.Driver");
     ~~~

### 创建对象的两种方法

1. 使用 Class 对象的 newInstance()方法执行空参构造器，要求该 Class 对象对应的类有默认的空参构造器
2. 先使用Class 对象获取指定的Constructor 对象，再调用Constructor 对象的 newInstance方法

~~~java
//获取Person 类的 Class 对象
Class clazz=Class.forName("com.company.Person");
//使用newInstane方法创建对象
Person p=(Person) clazz.newInstance();
//获取构造方法并创建对象
Constructor c=clazz.getDeclaredConstructor(String.class,String.class,int.class);
//创建对象并设置属性
Person p1=(Person) c.newInstance("李四","男",20);
~~~

### 数组

Class类的isArray方法检查给定的Class对象是否为数组。通过getComponentType方法获取数组元素类型。进一步分析或者动态创建数组使用java.lang.reflect包中的Array类。

~~~java
// 子类[]可以转型为父类[]，也可以转型为Object
public static Object goodCopyOf(Object a, int newLength){
    Class c1 = a.getClass();
    if(!c1.isArray())
        return null;
    Class componentType = c1.getComponentType();
    int length = Array.getLength(a);
    Object newArray = Array.newInstance(componentType, newLength);
    System.arraycopy(a, 0, newArray, 0, Math.min(length, newLength));
    return newArray;
}
~~~

### 动态代理

JDK动态代理是通过java.lang.reflect包中Proxy类的newProxyInstance方法生成代理类对象。对被代理对象的**接口**方法进行增强。

~~~java

private static Object getProxy(Object obj) throws IllegalArgumentException {
    return Proxy.newProxyInstance(
        // 加载器
        obj.getClass().getClassLoader(),
        // 接口
        obj.getClass().getInterfaces(),
        // InvocationHandler函数式接口
        (proxy, method, args) -> {
            System.out.println("invoke before");
            Object result = method.invoke(obj, args);
            System.out.println("invoke after");
            return result;
        });
}
~~~

JDK动态代理是针对接口的动态代理，生成继承了Proxy类并实现了目标接口的匿名类对象。该类以InvocationHandler为构造参数，在静态初始化块中通过反射获得接口的目标方法，调用InvocationHandler的invoke方法实现代理方法。

针对类的实现代理可以使用第三方库cglib，主要是对指定的类生成一个子类，覆盖其中的方法，并覆盖其中方法的增强，但是因为采用的是继承，所以该类或方法最好不要生成final，对于final类或方法，是无法继承的。

### 泛型

泛型的本质是为了参数化类型，也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。相较于Object可以做一致性限制等。

* 泛型方法：在方法返回值前添加类型参数声明，该方法在调用时可以接收不同类型的参数。根据传递给泛型方法的参数类型，编译器适当地处理每一个方法调用。

* 泛型类、泛型接口：在类名、接口名后面添加类型参数声明。和泛型方法一 样，泛型类、泛型接口的类型参数声明部分也包含一个或多个类型参数，参数间用逗号隔开。

  ~~~java
  //此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
  //在实例化泛型类时，必须指定T的具体类型
  public class Generic<T>{ 
      //key这个成员变量的类型为T,T的类型由外部指定  
      private T key;
  
      public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定
          this.key = key;
      }
  
      public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
          return key;
      }
  }
  ~~~

* 上下边界：类型实参只准传入某种类型的父类或某种类型的子类。<? extends T>表示该通配符所代表的类型是 T 类型的子类，<? super T>表示该通配符所代表的类型是 T 类型的父类。PECS指“Producer Extends，Consumer Super”。

* 类型擦除：Java 中的泛型基本上都是在编译器这个层次来实现的。在生成的 Java 字节代码中是不包含泛型中的类型信息的。如果指定了类型参数的上界的话，则使用这个上界。泛型类和泛型方法的声明并没有擦除，可以在通过放射获取。

* 数组：Java 中不能创建一个确切的泛型类型的数组。

  ~~~java
  // List<String>[] ls = new ArrayList<String>[10];  // error
  ~~~

  

### 注解



![Java注解 (2)](pics/Java%E5%9F%BA%E7%A1%80/Java%E6%B3%A8%E8%A7%A3%20(2).png)



## 异常与日志处理

### 异常继承关系

![image-20210824202415009](pics/Java%E5%9F%BA%E7%A1%80/image-20210824202415009.png)

### try-with-resource

ry-with-resources语句是一种声明了一种或多种资源的try语句。资源是指在程序用完了之后必须要关闭的对象。try-with-resources语句保证了每个声明了的资源在语句结束的时候都会被关闭。

~~~java
static String readFirstLineFromFile(String path) throws IOException {
    try (BufferedReader br =
                  new BufferedReader(new FileReader(path))) {
        return br.readLine();
    }
}
~~~

* 任何实现了java.lang.AutoCloseable接口的对象，和实现了java.io.Closeable接口（AutoCloseable的子接口）的对象，都可以当做资源使用。
* 资源关闭顺序与初始化顺序相反

### Objects.requireNonNull方法

Objects.requireNonNull可以提前判断是否为null，将NPE提前抛出。同样的防御性编程，还可以使用默认禁用的断言assert，为通过则抛出AssertionError。

~~~java
Objects.requireNonNull(x);
assert x>0;
~~~

### 日志处理

Java 的日志管理系统主要通过日志记录器和日志处理器实现。

~~~java
// 将通过默认日志处理器ConsoleHandler打印到System.err流
Logger.getGlobal().info("Hello Logger Info");
~~~

* 日志记录器：可以自定义日记记录器，日志记录器和包不一样，它是有层次的。

* 日志分7种级别：SEVERE、WARNING、INFO、CONFIG、FINE、FINER、FINEST。记录器默认记录前三种级别日志。

* 日志处理器：可以自定义日志处理器。处理器也要相应的日志级别，默认处理前三种级别日志。

  ~~~java
  // 获取名字为com.mycompany.myapp的日志记录器
  Logger logger = Logger.getLogger("com.company.myapp"); // "com.company"记录器禁用也会禁用此记录器
  // 设置日志管理器的日志级别，记录所有级别
  logger.setLevel(Level.ALL);  
  // 禁用记录器的继承自父记录器的处理器
  logger.setUseParentHandlers(false);
  FileHandler fileHandler = null;
  try {
      // 自定义日志处理器
      fileHandler = new FileHandler("/configLog.log");
      fileHandler.setLevel(Level.CONFIG);
  } catch (IOException e) {
      e.printStackTrace();
  }
  // 使用新的日志处理器
  logger.addHandler(fileHandler);	
  logger.config("Hello Logger Cofig");
  ~~~

* 日志记录器和处理器，可以通过setFilter方法安装过滤器。日志处理器可以通过setFormatter方法安装格式化器
* 通过编辑配置文件，可以改变日志管理系统的不同属性。默认配置文件位于jre/logging.properties。当然也通过java -Djava.util.logging.config.file来修改

