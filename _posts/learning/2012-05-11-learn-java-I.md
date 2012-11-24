---
layout: post
title: "Thinking in Java (I)"
category: learning
tags: [java]
description: |
  java的经典名著，无需过多解释，没仔细研读过，别说你学过java，随手翻翻也感觉受益匪浅，java是我喜欢的语言之一，Thinking in Java也是我看过的最好的技术书籍之一。
---
{% include JB/setup %}

##第1章 对象导论

面向对象的三要素：封装、继承、多态。

封装是将事物抽象为类，隐藏具体实现。命令式语言是对汇编语言的抽象，汇编是对底层机器的抽象，面向对象语言是对所解决问题的抽象。


两种方法使基类和导出类产生差异：子类添加新方法、子类复写父类中的方法。

在处理类型的层次结构时，把对象当做基类的对象来处理，这样代码就能脱离具体类型。面向对象函数调用采用后期绑定，相对象发送消息时，被调用的代码直到运行时才能知道调用方法的绝对地址，编译器只确保方法存在和类型检查。后期绑定即动态绑定，这是实现多态的前提。面向基类编程的过程称为向上转型，多态可以使事物总是被正确的处理。

单根继承性：java所有的类都继承自单一的基类Object，单根继承性使得所有的对象都可以执行一些基本的操作。

向上转型是安全的，参数化类型，即泛型消除了向下转型的犯错误的可能。

对象数据的两种存储方式：堆栈（stack）和堆（heap）。C++的对象数据存放在堆栈中，这样得到了最大的执行速度，丧失了灵活性，对象的存储空间和生命周期都在程序编写时确定。java对象数据在堆内存池中动态创建，垃圾回收机制在对象不被使用时自动销毁，释放内存。

原始的web服务（服务器—浏览器）是一个简单的单项过程，即客户端向服务器发出一个请求，服务器返回一个文件，客户端解读文件。html只提供简单的 数据收集的作用，数据提交通过CGI（common gateway interface）传递 。客户端变成合理利用了客户机的资源，客户端编程的方法：插件、脚本语言。

applet提供了一中软件分发的方法，最终由于微软的封杀，被人遗忘。Flex以及Silverlight是一种applet的备选方案。Silverlight只局限于windows平台。移动平台似乎不喜欢插件，由于苹果的封杀，Flash穷头陌路。但是存在于98%的web浏览器上的Flash player，还有整个Adobe的多媒体生态环境的支撑，Flex短时间内还将是一个不错的富客户端解决方案。插件比脚本语言的优点在于开发者在编程时无需考虑浏览器的相关性。但是浏览器无插化是个趋势，html5、CSS3和javascript的完美结合将会是移动和PC平台web前段统一的解决方案。

##第2章 一切都是对象

java对象操作的方式是引用。

数据存储方式：

1. 寄存器，速度最快，不能直接控制，按需分配，C/C++可以向编译器建议寄存器分配方式。
2. 堆栈，位于通用RAM（随机访问存储器），速度仅次于寄存器，但是要使用它，程序创建时系统必须知道存储在堆栈中的所有项的确切生命周期，这限制了程序的灵活性，所以java只将对象的引用和基本类型信息存放在堆栈中，这也是鼓励使用基本类型的原因。
3. 堆，通用的内存池（也位于RAM），用于存放java的所有对象数据，不同于堆栈的是，new一个对象后，当程序执行时才在堆中进行存储分配，当然这种灵活性的代价是用堆存储分配和清理要比堆栈消耗的时间多。
4. 常量存储，常量值通常直接存放在程序代码内部，由于他们不会被改变，所以是安全的。有时嵌入式系统中分离的常量也存放在ROM（只读存储器）中。
5. 非RAM存储，数据也可以完全存活于程序之外，这样可以相对独立于程序，例如流对象和持久化对象。在流对象中对象转化成字节流发送到其他机器。在持久化对象中，对象被存放在磁盘上，这种存储方式的好处是对象存放在其他媒介上，在需要时可以恢复，例如JDBC、Hibernate。

当申明一个事物是static时，这个域或方法不与对象实例相关联。通过类可以直接调用static方法或域，一个static字段对每个类来说只有一份存储空间，非static字段每个对象对应一份存储空间。static方法在对象未创建前可以调用（main()方法）。

javadoc的命令都只能在“/**”注释出现时，同样结束于“*/”。
一个例子：
{% highlight java %}
/** 计算阶乘。
* 执行方法为 java Factorial [param]
* @author Calvin
* @author www.mceiba.com
*/
public class Factorial{
     /** 主函数
     * @param args 计算阶乘的参数
     */
     public static void main(String[] args){
          int input=Integer.parseInt(args[0]);
          //int input=4;
          double result=factorial(input);
          System.out.println(result);
     }
     /** 函数体 */
     public static double factorial(int x){
          if(x<0)
               return 0.0;
          double fact=1.0;
          while(x>1){
               fact=fact*x;
               x-=1;
          }
          return fact;
     }
}
{% endhighlight %}

需要注意一点的是中文情况下使用javadoc命令需要设置字符集：
{% highlight java %}
javadoc -encoding UTF-8 -charset UTF-8  Factorial.java
{% endhighlight %}

##第3章 操作符

不可忽略的**赋值语句**：基本数据类型的赋值分配了新的存储空间，因为基本类型存储的是实际的数值；对一个对象的操作实际上是对对象引用的操作，因此对象的赋值只相当于给对象起了一个别名。将一个对象传递给方法，实际上传递的是一个引用，就如同C/C++中的变量名前加`&`，改变的是方法之外的对象。

C++和java中的**随机数**生成：C++中用`rand()`生成随机数，其实是伪随机数，因为它默认种子为1，不接收参数，每次产生的随机数序列是一样的，要传递种子产生随机数用`srand()`，常用当前时间作为种子，即`srand(time(0))`；java刚好相反，提供了一个产生随机数的类`Random`，而默认就是以当前时间为种子的，要是想产生相同的随机数序列，可以指定固定的种子。
{% highlight java linenos %}
import static java.lang.System.out;
import java.util.*;
public class Rand{
     public static void main(String[] args){
          Random rand=new Random(3);
          for(int i=0; i<10; i++){
               //产生1~100之间的随机数
               out.println((rand.nextInt(100)+1)+"\t");
          }
          for(int i=0; i<10; i++){
               //nextFloat产生float类型的随机数
               out.println(rand.nextFloat()+"\t");
          }
     }
}
{% endhighlight %}

自增自减的规律：前缀先生成值，后缀后生成值。
测试对象等价性的问题，关系操作符`==`和`!=`用于基本数据类型时比较的值得大小，用于对象时比较的是对象的引用，要比较对象的值用`equals()`，但是不适合基本类型。实际上默认的`equals()`方法（它是Object的方法）并不能比较两个对象的值，只是大多数的类都重写了该方法，猜一下下边程序的结果：
{% highlight java linenos %}
int a=1;int b=1;
out.println(a==b);
Integer aa=new Integer(1);
Integer bb=new Integer(1);
out.println(aa==bb);
out.println(aa.equals(bb));
aa=bb;
out.println(aa==bb);
Value va=new Value();
Value vb=new Value();
va.v=vb.v=1;
out.println(va.equals(vb));
out.println(va.v==vb.v);
{% endhighlight %}

如果你猜的没错，那结果应该是`true, false, true, true, false, true`。

直接常量后面的后缀字符（`l, f, d`及大写）标志了它的类型，java整数默认只支持十进制、十六进制（`0x, 0X`）、八进制（`0`），不能直接表示二进制，但是任意整数都可以使用`Integer.toBinaryString()`来显示二进制结果。

java的移位操作：
{% highlight java linenos %}
int s=8;
out.println(s<<2);
 int t=-8;
out.println(t>>2);
int r=-8;r>>>=2;     //无符号右移，高位插0
out.println(r);
{% endhighlight %}

值得注意的一点，对于布尔值按位操作符于逻辑操作符效果是一样的，只是按位操作符不会短路，逐位取反操作`~`只对符号位也起作用，如 `~1 = -2`，想得到期望的结果，可以`~(-1)`。

##第4章 控制执行流

java中不允许数字代表布尔值。java中唯一用到逗号操作符的是在for循环的控制表达式中，使用逗号操作符时，可以在for语句中定义多个变量，或执行多条步进语句，但是必须有相同的类型。

`return, continue, break`：return作用范围最大，可以跳出当前方法；普通的continue和break只能跳出当前循环；带标签的continue和break可以跳到标签位置。java中唯一需要用标签的地方就是嵌套循环。switch使用限制较多，选择因子只能是int或char这样的整数值，case后的break是可选的，但是会执行直到遇到break为止，default后的break是多余的。
{% highlight java linenos %}
//! 错误用法 out.println(True+4);
out.println((int)2.9);
out.println(Integer.MAX_VALUE+1);
//!if(1) out.println("1");
//逗号操作符
Random random=new Random();
for(int j=0, rand=0; ; rand=random.nextInt(10), out.println(rand), j++)
     if(rand>7)
          break;
int array[]={1,2,3,4,5,6,7,8,9,10};
for(int rand: array){
     out.print(rand+"\t");
}
//无穷循环
while(true){
     double rand=Math.random();
     out.println(rand);
     if(rand>0.9)
          break;
}
//标签及跳转
int i=0;
outer:
for(;;){
     inner:
     for(; i<10; i++){
          out.print(i+"\t");
          if(i==2) {out.println("continue"); continue;}
          if(i==3) {out.println("break"); i++; break;}
          if(i==7) {out.println("continue inner"); continue inner;}
          if(i==8) {out.println("break outer"); break outer;}
     }
}
label:
for(i=97;;i++){
     char ch=(char)i;
     switch(ch){
          case 'a': 
          case 'e':
          case 'm': out.print("m\t"); break;
          case 'n': out.print("n");
          case 'p': out.print("p"); break;
          case 'z': out.print("end"); break label;
          default: out.print("*\t");
     }
}
{% endhighlight %}

##第5章 初始化与清理

###构造器&初始化
在java中**初始化**和**创建**是捆绑在一起的，但是概念上将他们是彼此独立的。构造器没有返回值，这与返回值为空（void）不同。创建对象时实际上是返回一个对象的引用，而同时自动调用了构造器，如果构造器有返回值，就会与创建对象时返回的对象引用冲突，因为这两个过程是捆绑在一起的，没有办法分离，我想这就是构造器没有返回值的原因。

方法重载可以通过参数裂变来区分不同的方法，甚至参数顺序的不同也能区别两个方法，返回值的不同不能区分方法，因为忽略返回值的情况下会产生歧义。涉及基本类型的重载中，如果传入的方法类型小于声明中的方法类型，实际的数据类型会被提升（char可以提升为int），相反的过程不能通过编译。

自己创建构造器（无论有参无参），编译器都不会自动创建构造器。

this关键字表示对当前对象的引用，只能在内部使用，this可以用在返回当前的对象、将当前对象传递给其它方法、以及在构造其中调用构造器。使用this调用构造器需要注意：

- 使用this只能调用一个构造器，这是因为创建一个对象时也只能调用一个构造器来初始化，当然这个构造器可以自己实现，或者再调用其他构造器。
- 构造器调用必须放在起始位置，否则编译报错。
- 构造器只能是自动调用，或者由构造器调用，普通方法不能调用构造器，因为对象创建的生命周期中，构造方法只能调用一次，而普通方法只有在对象创建以后才能调用，而这时构造方法已经调用过了，就不能再调用。
{% highlight java linenos %}
import static java.lang.System.out;
import java.util.*;

class Person{
     public void eat(Apple apple){
          Apple peeled=apple.getPeeled();
          out.println(peeled.getState());
          out.println("yummy!");
     }
}
class Peeler{
     static Apple peel(Apple apple){
          apple.setState();
          return apple;
     }
}
class Apple{
     private String state="unpeel";
     public Apple getPeeled(){
          return Peeler.peel(this);
     }
     public void setState(){
          this.state="peeled";
     }
     public String getState(){
          return this.state;
     }
}
public class Test{
     public static void main(String[] args){
          class Leaf{
               Leaf(){};
               Leaf(int j){
                    out.println("get an int: "+j);
               }
               Leaf(String s){
                    out.println("get a String: "+s);
               }
               Leaf(int j, String s){
                    this(j);
                    //!this(s);
                    out.println("get a String: "+s);
               }
               int j=0;
               Leaf increment(){
                    j++;
                    return this;
               }
               void print(){
                    out.println("j= "+j);
               }
          }
          Leaf la=new Leaf();
          la.increment().increment().increment().print();
          Leaf lea=new Leaf(3, "leaf");
         
          new Person().eat(new Apple());    
     }
}
{% endhighlight %}

###终极处理&垃圾回收

java的垃圾回收器只会释放由new分配的内存，特殊的内存释放（如java中调用其他语言的代码），可以使用默认的`finalize()`函数（继承自Object），垃圾回收器回收动作发生时首先调用`finalize()`方法。

与 Java 不同，C++ 支持局部对象（基于栈）和全局对象（基于堆）。因为这一双重支持，C++ 提供了自动构造和析构，这导致了对构造函数和析构函数的调用，（对于堆对象）就是内存的分配和释放。在 Java 中，所有对象都驻留在堆内存，不存在局部对象，因此不需要析构来销毁局部对象。`finalize()`不同于C++的析构函数，JVM不一定会调用它，所以是不可靠的。使用`System.gc()`可以触发运行垃圾回收器，垃圾回收器会努力回收垃圾释放内存，但这并不意味着一定会执行`finalize()`。

java垃圾回收机制的策略是：程序濒临存储空间用完的时刻，垃圾回收器才会执行以释放内存，如果直到程序执行结束垃圾回收器也没有释放内存，那么随着程序的退出，内存会自动释放，交给操作系统。

使用`finalize()`的例子:
{% highlight java linenos %}
class Book{
     boolean checkedout=false;
     Book(boolean checkout){
          checkedout=checkout;
     }
     void checkin(){
          checkedout=false;
     }
     protected void finalize(){
          if(checkedout){
               out.println("Error: checked out!");
          }
     }
}
Book novel=new Book(true);
novel.checkin();
//Book boo=new Book(true);
//boo.finalize();
new Book(true);
//如果不使用匿名对象，System.gc()不一定触发finalize()，
//因为垃圾回收器不确定对象是否“存活”
System.gc();
{% endhighlight %}

###垃圾回收器的工作原理

垃圾回收器有效地提高了对象的创建速度，因为GC运行时一边释放内存，一边使堆中的对象存储更紧密，对对象进行重新排列，提高存取速度（“堆指针”更接近地址入口）。

java的自适应垃圾回收技术：JVM进行监视，如果所有对象都很稳定，垃圾回收器的效率降低的话，就切换到**标记-清扫**方式；同样，JVM跟踪**标记-清扫**的效果，要是堆内出现很多碎片，就会切换回**停止-复制**方式。

###初始化

java尽力保证所有变量在使用前都初始化，对于局部变量未初始化的调用会产生编译错误。类的成员变量在对象创建时会得到一个默认的初始值（0或null），而且初始化的顺序是按照定义的顺序，即使是变量定义在调用方法之后，任然会先初始化变量。、

静态数据会默认得到一个初始化（如果没有对它进行初始化），需要注意的是`static`关键字不能用在局部变量。静态数据变量也能被修改，但只能是静态的修改。
{% highlight java linenos %}
class Spoon{
     static int i;
     static { i=47; }
     //!i=47;
}
{% endhighlight %}

非静态实例初始化时可以有静态初始化一样的语法，只不过没有static关键字，而且实例化是在构造器之前执行。

###可变参数列表

在参数个数类型未知的情况下创建一Object数组为参数的方法，这得益于java的单根继承性。
{% highlight java linenos %}
class VarArgs{
     static void printArray(Object[] arg){
          for(Object ar: arg)
               out.print(ar+"\t");
          out.println();
     }
}

public class Test{
     public static void main(String[] args){
          VarArgs.printArray(new Object[]{1, 2, 'a', "spam", new Date()});
          //!VarArgs.printArray();
     }
}
{% endhighlight %}
这种方法也有不便之处，就是得遵循数组的语法，Java SE5以后加入了更好的语法，同样的功能，上边的代码可以这么写（而且对以上的语法是兼容的）：
{% highlight java linenos %}
class VarArgsNew{
     static void printArray(Object...arg){
          for(Object obj: arg)
               out.println(obj+":"+obj.getClass());
     }
     static void printArrayString(int req, String...arg){
          //!out.println((req+1)+":"+req.getClass());
          out.println((req+1)+":");
          for(Object obj: arg)
               out.println(obj+":"+obj.getClass());
     }
}

public class Test{
     public static void main(String[] args){
          VarArgsNew.printArray(1, new Integer(2), 'a', "spam", new Date());
          VarArgsNew.printArray(1, new Integer(2), 'a', "spam", new Date());
          //还可以传递空值
          VarArgsNew.printArray();
          //可以限制传入类型
         VarArgsNew.printArrayString(1, "spam", new Date().toString());
     }
}
{% endhighlight %}

参照结果可以发现，在单一混合类型的参数列表中，自动包装机制有选择的的将基本类型提升为它的包装类，而在有明确类型要求的参数列表中，则不会。

###枚举类型

枚举可以看做一个特殊的类，它也有自己的方法（`ordinal(), static values()`）等，enum与switch语句可以很好的组合，扩展switch的一些功能：
{% highlight java linenos %}
enum Fruit{
     APPLE, ORANGE, PEAR
}
class EatFruit{
     Fruit fruit;
     EatFruit(Fruit fruit){
          this.fruit=fruit;
     }
     public void eat(){
          out.print("eating ");
          switch(fruit){
               case APPLE: out.println(fruit.APPLE); break;
               case ORANGE: out.println(fruit.ORANGE); break;
               case PEAR: out.println(fruit.PEAR); break;
               default: out.println("no eating");
          }
     }
}

public class Test{
     public static void main(String[] args){
          EatFruit 
          eta=new EatFruit(Fruit.APPLE),
          eto=new EatFruit(Fruit.ORANGE),
          etp=new EatFruit(Fruit.PEAR);
          eta.eat();
          eto.eat();
          etp.eat();
     }
}
{% endhighlight %}

##第6章 访问权限控制

控制对成员的访问权有两个原因：

- 为了使用户不要去触碰那些他们不该接触的部分，这些部分对原类进行内部操作是必要的，但是它并不属于客户端程序所需接口的一部分。
- 是为了让类库设计者可以更改类的内部工作方式，而不必担心这样会对客户端程序产生重大的影响，这也是主要的原因。访问控制权限可以确保不会有任何客户端程序依赖于某个类的底层实现。

访问权限控制的等级从高到低以依次为：public、protected、包访问权限（没有关键字）和private。

<table width="100%" align="center" border="1">
     <tr><td></td><td>同一个类</td><td>同一个包</td><td>不同包的子类</td><td>不同包的非子类</td></tr>
     <tr><td>private</td><td>√</td><td></td><td></td><td></td></tr>
     <tr><td>default</td><td>√</td><td>√</td><td></td><td></td></tr>
     <tr><td>protected</td><td>√</td><td>√</td><td>√</td><td></td></tr>
     <tr><td>public</td><td>√</td><td>√</td><td>√</td><td>√</td></tr>
</table>

java的包提供了一个命名空间的机制，不同类中相同的方法（参数也相同）不会冲突，因为有类名的限制，但是相同的类名只能通过包来加以区别。组织包用package关键字，如果不使用，就会有一个默认包（当前目录），即一个未命名包（按照惯例，包名的第一部分为类创建者的反顺序的域名，第二部分为类的文件组织目录）。一个java源代码文件称为一个编译单元，每个编译单元只能有一个（可以没有）public类，而且类名必须与文件名相同。如果编译单元中还有其他的类的话，那么在包外是不可见的，就是包访问权限级别，而且他们主要是用来为主public类提供支持的。

java解释器的运行过程：

1. 找出环境变量CLASSPATH用作查找.class文件的根目录。
2. 从根目录开始解释器获取包的名称，并把每个句点替换成反斜杠，以产从CLASSPATH根中产生一个路径名称。
3. 得到的路径名称会与CLASSPATH中的不同项连接，解释器就在这些目录中查找相关的.class文件。
{% highlight java linenos %}
//这是一个使用private访问权的例子
class Sundae{
     //单例模式
     //不能被继承，始终只能创建它的一个对象
     private Sundae(){}
     private static Sundae sun=new Sundae();
     public static Sundae makeSundae(){
          return sun;
     }
}

public class IceCream{
     public static void main(String[] args){
          //!Sundae sun=new Sundae();
          Sundae sun=Sundae.makeSundae();
     }
}
{% endhighlight %}

访问权限的控制常常被称作是**具体实现的隐藏**。把数据和方法包装进类中，以及具体实现的隐藏，一起被称为**封装**。

将接口和实现分开的好处是，客户端程序员除了向接口发送消息外什么也不能做，而随意的修改不是public的东西也不会破坏客户端的代码。

类的访问权限的一些限制：

- 每个编译单元只能有一个public类
- public类的名称必须完全与文件名相同，包括大小写
- 编译单元可以没有public类，这样也就没有了类名与文件名相同的限制，但是只有包访问权限

##第7章 复用类

类复用的三种方式：**组合**、**继承**和**代理**。

类中的域（属性）都是以组合方式实现了复用，编译器默认将基本类型初始化为零，对象则初始化为null，如果需要自己初始化，可以在以下位置进行：

- 在类定义的地方，先于构造器初始化。
- 构造其中。
- 使用对象之前（惰性初始化）。
- 使用实例初始化。

创建一个类时，总是在继承，除非明确指明基类，否则隐式的继承java的单一根类Object。继承使用`extends`关键字，继承时会自动得到父类的所有域和方法。

java会在子类的构造器中自动插入（靠前插入）默认构造器（无参）父类的构造器，父类的构造器优先调用，而且总会调用，带参数的构造器需要使用`super`关键字显示调用。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
class Art{
     Art(){
          println("Art constructor");
     }
     Art(String name){
          println("Art constructor : "+name);
     }
}
class Drawing extends Art{
     Drawing(){
          println("Drawing constructor");
     }
     Drawing(String name){
          super(name);
     }
     protected String name="Spam";
}
public class Cartoon extends Drawing{
     Cartoon(){
          println("Cartoon constructor");
     }
     Cartoon(String name){
          super(name);
     }
     public static void main(String[] args){
          Cartoon ct=new Cartoon();
          Cartoon ctn=new Cartoon("Spam");
          println(ctn.name);
     }
}

//out:
//Art constructor
//Drawing constructor
//Cartoon constructor
//Art constructor : Spam
//Spam
{% endhighlight %}

java默认并没有对代理提供支持，这是继承和组合之间的中庸之道。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
class Controls{
     void up(int velocity) { println("up: "+velocity); }
     void down(int velocity) { println("down: "+velocity); }
     void left(int velocity) { println("left: "+velocity); }
     void right(int velocity) { println("right: "+velocity); }
}
class SpaceShip extends Controls{
     private String name;
     public SpaceShip(String name) { this.name=name; }
     public String toString(){ return name; }
     public static void main(String[] args){
          SpaceShip protector=new SpaceShip("NSEA Protector");
          protector.up(100);
     }
}
public class Detergent{
     private String name;
     private Controls controls=new Controls();
     public Detergent(String name) { this.name=name; }
     public void up(int velocity) { controls.up(velocity); }
     public void down(int velocity) { controls.down(velocity); }
     public void left(int velocity) { controls.left(velocity); }
     public void right(int velocity) { controls.right(velocity); }
     public static void main(String[] args){
          Detergent protector=new Detergent("NSEA Protector");
          protector.up(100);
     }
}
{% endhighlight %}

上例中用代理实现了与继承同样的接口，但是使用代理可以拥有更多的控制力，我们可以只提供对象成员方法的一个子集。一些情况下我们也会结合的使用继承和组合。继承中有时会遇到自己清理垃圾的情况，这时候需要注意调用的顺序，往往是先调用自己的清理方法，再调用父类的清理方法，就如同C++中的析构函数，经常需要放在`finally{}`中，而不是`finalize()`中。重载在继承中任然有效。使用`@Override`注解可以防止在复写时以外的进行了重载的情况（不能通过编译）。

###组合与继承之间的选择

组合与继承都允许在新的类中放置子对象，只不过一个是显示，一个是隐式。

- 组合常用于想在新类中使用现有类的功能，而非它的接口这种情况。嵌入一个现有类的private对象，新类中使用了现有类的功能，但是新类的用户看到的只是新类所定义的接口，而非嵌入类的接口。有时隐藏成员对象自身的具体实现，而将成员对象声明为public是安全的，而且有时具有特别的意义，比如：
 {% highlight java linenos %}
class Engine{
     public void start(){}
     public void rev(){}
     public void stop(){}
}
class Wheel{
     public void inflate(int psi){}
}
class Window{
     public void rollup(){}
     public void rolldown(){}
}
class Door{
     public Window window=new Window();
     public void open(){}
     public void close(){}
}
public class Car{
     public Engine engine=new Engine();
     public Window[] window=new Window[4];
     public Door
          left=new Door(),
          right=new Door();
     public Car(){
          for(int i=0; i<4; i++)
               wheel[i]=new Wheel();
     }
     public static void main(String[] args){
          car car=new Car();
          car.left.window.rollup();
          car.wheel[0].inflate(72);
     }
}
{% endhighlight %}

- 使用继承的时候通常意味着你是使用一个通用类，并为了某种需要而需要将其特殊化，是一个从一般到特殊化的过程。

使用组合还是转型最清晰的判断方法是**是否需要从新类向基类进行向上转型**。

###向上转型

“为新类提供方法”并不是继承技术中最重要的方面，其最重要的方面是用来表现新类和基类之间的关系，即**新类是现有类的一种类型**。继承可以保证父类中的所有方法在子类中也有效，所以能够向基类发出的消息同样也可以向子类发送，由于向上转型是专用类向通用类转型，所以是安全的，向上转型是多态性的基础。
 {% highlight java linenos %}
import static com.mceiba.util.Print.*;
class Language{
     void lPrint(){}
}
class Java extends Language{
     void lPrint(){
          println("System.out.println(\"Hello World!\")");
     }
}
class Python extends Language{
     void lPrint(){
          println("print(\"Hello World!\")");
     }
}
public class Upcast{
     public static void lPrint(Language lg){
          lg.lPrint();
     }
     public static void main(String[] args){
          Java java=new Java();
          Python python=new Python();
          lPrint(java);
          lPrint(python);
     }
}
//out:
//System.out.println("Hello World!")
//print("Hello World!")
{% endhighlight %}

###final关键字

`final`通常指的是无法改变，使用final的三种情况：数据、方法和类，通常是出于**设计**和**效率**的考虑。一个既是static有是final的域是一段不能改变的存储空间（一般用大写表示，即编译期常量），对于基本数据类型，final意味着数值恒定不变（定义时必须赋值），对于对象，意味着引用恒定不变（对象本身是可以修改的）。java允许**空白final**的存在，即声明为final但未给初值的域，但是编译器要确保空白final在使用前被初始化。这样空白final可以用在需要根据对象不同而有所不同，却又保持恒定不变的特征。
 {% highlight java linenos %}
import static com.mceiba.util.Print.*;

public final class Empty{
     private static final float PI=3.14f;
     private final float r;
     public Empty(){
          r=1f;
     }
     public Empty(final float r){
          this.r=r;
     }
     public void area(){
          println("This area is "+PI*r*r);
     }
     public static void main(String[] args){
          new Empty().area();
          new Empty(3).area();
     }
}
{% endhighlight %}

在参数列表中也可以将参数声明为final，这意味着无法在方法中修改参数的值或者参数所指向的引用。

使用final方法的两个原因是：

- 把方法锁定，以确保继承类中使方法行为保持不变，并且不会被覆盖。
- 早期的java版本中出于效率考虑，作为内联调用，现在由于JVM的自动优化，已经不需要了。

类中所有private方法都隐式的指定为final，但是好像没什么必要。final类意味着该类永远不需要修改，也不能有子类。

在带有继承的类中（实际上是所有的类），JVM总是试图首先访问`main()`方法，加载器开始启动加载编译代码，如遇到基类，就首先加载基类（防止子类的初始化对基类的依赖），加载完成后就可以创建对象了。首先是对象中所有的基本类型设为默认值，对象引用设为null，然后基类的构造器会被调用，基类构造器完成之后实例变量按次序被初始化，最后执行构造器的其余部分。

##第8章 多态

**封装**通过合并特征和行为来创建新的数据类型。**实现隐藏**则通过将细节**私有化**把接口和实现分离开来。**多态**（也称作动态绑定、后期绑定或运行时绑定）的作用是消除类型之间的耦合关系。

java中除了static方法和final方法（private方法属于final方法）外，其他方法都是后期绑定。方法声明为final可以防止被覆盖，但更重要的“关闭”了动态绑定。动态绑定是多态的基础，所以静态方法和final方法（私有方法）是不具有多态性的，而且所有访问域的操作也都是没有多态性的，因为域的操作时是由编译器来解析的，它在编译后就已经确定了。在编译之前对象是作为它的引用类型（转型过以后的类型）来处理的，所以不满足多态性的方法是作为转型后的类型的对象来发生行为的。
 {% highlight java linenos %}
import static com.mceiba.util.Print.*;

class Circle extends Shape{
     public String name="Circle";
     public void draw(){
          println("Drawing Circle");
     }
     public static void getType(){
          println("Type: Circle");
     }
}
class Square extends Shape{
     public String name="Square";
     public void say(){
          println("I'm Shape");
     }
     public void draw(){
          println("Drawing Square");
     }
}

public class Shape{
     public String name="Shape";
     private void say(){
          println("I'm Shape");
     }
     public void draw(){
          println("Drawing Shape");
     }
     public static void getType(){
          println("Type: Shape");
     }
     public static void main(String[] args){
          Shape shape=new Shape();
          Shape circle=new Circle();
          Shape square=new Square();
          println("Name: shape->"+shape.name+", circle->"+circle.name+", square->"+square.name);
          println("*****draw()*****");
          shape.draw();
          circle.draw();
          square.draw();
          println("*****static: circle.getType()*****");
          circle.getType();
          println("*****Shape private: square. say()*****");
          square. say();
     }
}

//out:
//Name: shape->Shape, circle->Shape, square->Shape
//*****draw()*****
//Drawing Shape
//Drawing Circle
//Drawing Square
//*****static: circle.getType()*****
//Type: Shape
//*****Shape private: square. say()*****
//I'm Shape
{% endhighlight %}

在一个类中，构造器隐式的声明为static，private方法隐式声明为final，因此都是不具备多态性的。创建对象时总是优先调用父类的构造器，其次才参考当前类中域的声明顺序，即复杂对象调用构造器遵循下面的顺序（对象销毁的顺序与此相反）：

1. 在其他事情发生之前，将分配给对象的存储空间初始化为0（或者null）。
2. 调用基类构造器，这个过程是不断递归调用。
3. 按声明顺序调用成员的初始化方法。
4. 调用导出类构造器的主体。

模拟引用计数的例子：
 {% highlight java linenos %}
import static com.mceiba.util.Print.*;

class Shared{
     private int refcount = 0;
     private static long counter = 0;
     private final long id = counter++;
     public void shared(){
          println("Creating "+this);
     }
     public void addRef() { refcount++; }
     protected void dispose(){
          if(--refcount == 0){
               println("Disposing "+this);
          }
     }
     public String toString() { return "Shared "+id;}
}
class Composing{
     private Shared shared;
     private static long counter = 0;
     private final long id = counter++;
     public Composing(Shared shared){
          println("Creating "+this);
          this.shared = shared;
          this.shared.addRef();
     }
     protected void dispose(){
          println("disposing "+this);
          shared.dispose();
     }
     public String toString() { return "Composing "+id; }
}

public class RefCounting{
     public static void main(String[] args){
          Shared shared = new Shared();
          Composing[] composing = {
               new Composing(shared),
               new Composing(shared),
               new Composing(shared),
               new Composing(shared),
               new Composing(shared),
               new Composing(shared),
               new Composing(shared)
          };
          for(Composing c: composing) c.dispose();
     }
}
//out:
//Creating Composing 0
//Creating Composing 1
//Creating Composing 2
//Creating Composing 3
//Creating Composing 4
//Creating Composing 5
//Creating Composing 6
//disposing Composing 0
//disposing Composing 1
//disposing Composing 2
//disposing Composing 3
//disposing Composing 4
//disposing Composing 5
//disposing Composing 6
//Disposing Shared 0
{% endhighlight %}

设计构造器的准则：**用尽可能简单的方法使对象进入正常状态；如果可以的话，尽量避免调用其他方法**。构造器内唯一能够安全调用的方法是基类的final（或private）方法，因为这些方法不能被覆盖，所以就不会出现转型的尴尬。
 {% highlight java linenos %}
import static com.mceiba.util.Print.*;

class Glyph{
     public void draw(){
          println("Glyph.draw()");
     }
     public Glyph(){
          println("Glyph() before draw()");
          draw();
          println("Glyph() after draw()");
     }
}
class RoundGlyph extends Glyph{
     private int radius = 1;
     public RoundGlyph(int r){
          radius = r;
          println("RoundGlyph(), radius = "+radius);
     }
     public void draw(){
          println("RoundGlyph.draw(), radius = "+radius);
     }
}

public class PolyConstructors{
     public static void main(String[] args){
          new RoundGlyph(5);
     }
}
//out:
//Glyph() before draw()
//RoundGlyph.draw(), radius = 0
//Glyph() after draw()
//RoundGlyph(), radius = 5
{% endhighlight %}

状态模式使我们能够在运行期间获得动态灵活性，既可以动态改变对象的状态。一条通用的准则为，**用继承表达行为间的差异，并用字段表达状态上的变化**。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;

class Actor{
     public void act() {}
}
class HappyActor extends Actor{
     public void act() { println("HappyActor"); }
}
class SadActor extends Actor{
     public void act() { println("SadActor"); }
}
class Stage{
     private Actor actor = new HappyActor();
     public void change() { actor = new SadActor(); }
     public void performPlay() { actor.act(); }
}
public class Transmogrify{
     public static void main(String[] args){
          Stage stage = new Stage();
          stage.performPlay();
          stage.change();
          stage.performPlay();
     }
}
//out:
//HappyActor
//SadActor
{% endhighlight %}

向上转型是自然的，但却会造成信息丢失；先下转型需要强制转型，而且是不安全的，如果所转类型是正确的类型，则转型成功，否则会抛出一个`ClassCastException`异常。

##第9章 接口

**接口**和**内部类**为我们提供了一种将接口与实现分离的更加结构化的方法。

**抽象类**是对普通基类的一般抽象，它允许不完全的抽象，即抽象类中某些方法可以有具体实现。**interface**关键字产生一个完全抽象的类，不提供任何的实现。

- 接口中的域隐式声明为public、static和final，可以被被非常量表达式初始化，但是不能是**空final**。
- 接口中的方法默认是public，而且必须是（无论有没有显示声明）。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
interface Instrument{
     int VALUE = 5;
     void play();
     void adjust();
     String what();
}
abstract class Wind implements Instrument{
     public abstract void play();
     public abstract void adjust();
     public String variety(){ return "Wind"; }
     public String what(){ return variety()+": "+this.getClass().getSimpleName(); }
}
class Brass extends Wind{
     public void play(){
          println("Brass.play()");
     }
     public void adjust(){
          println("Brass.adjust()");
     }
}
class WoodWind extends Wind{
     public void play(){
          println("WoodWind.play()");
     }
     public void adjust(){
          println("WoodWind.adjust()");
     }
}
public class Music{
     public void tune(Instrument it) { it.play(); }
     public void describe(Instrument it) { println(it.what()); }
     public void tuneAll(Instrument[] its){
          for(Instrument it : its){
               describe(it);
               tune(it);
          }
              
     }
     public static void main(String[] args){
          Instrument[] its = {
               new Brass(),
               new WoodWind()
          };
          new Music().tuneAll(its);
     }
}
//out:
//Wind: Brass
//Brass.play()
//Wind: WoodWind
//WoodWind.play()
{% endhighlight %}

**策略模式**：创建一个能够根据所传递的参数对象的不同而具有不同行为的方法。策略模式可以使处理问题的方法和所处理的问题之间完全解耦。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;

interface Processor{
     String name();
     Object process(Object input);
}
class StringProcessor implements Processor{
     public String name(){
          return getClass().getSimpleName();
     }
     public Object process(Object input) { return input; }
}
class Upcase extends StringProcessor{
     public String process(Object input){ //Covariant return
          return ((String)input).toUpperCase();
     }
}
class Downcase extends StringProcessor{
     public String process(Object input){ //Covariant return
          return ((String)input).toLowerCase();
     }
}
class Splitter extends StringProcessor{
     public String process(Object input){ //Covariant return
          return Arrays.toString(((String)input).split(" "));
     }
}
public class Strategy{
     public static void process(Processor pro, Object obj){
          println("Using Processor "+pro.name());
          println(pro.process(obj));
     }
     public static String str = "Beautiful is better than ugly";
     public static void main(String[] args){
          process(new Upcase(), str);
          process(new Downcase(), str);
          process(new Splitter(), str);
     }
}
//out:
//Using Processor Upcase
//BEAUTIFUL IS BETTER THAN UGLY
//Using Processor Downcase
//beautiful is better than ugly
//Using Processor Splitter
//[Beautiful, is, better, than, ugly]
{% endhighlight %}

但是，经常碰到的问题是想要使用的类是你无法修改的，因为大多数情况下使用的接口都不是自己创建的。这时，可以使用**适配器模式**，适配器中的代码将接受你所拥有的接口，并产生你所需要的接口。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;

class Filter{
     public String name(){
          return getClass().getSimpleName();
     }
     public Wavaform process(Wavaform input) { return input; }
}
class FilterAdapter implements Processor{
     Filter filter;
     public FilterAdapter(Filter filter) { this.filter = filter; }
     public String name(){ return filter.name(); }
     public Wavaform process(Object input){
          return filter.process((Wavaform)input);
     }
}
public class Adapter{
     public static void main(String[] args){
          Strategy.process(new FilterAdapter(new LowPass(1.0)), new Wavaform());
          Strategy.process(new FilterAdapter(new HighPass(2.0)), new Wavaform());
          Strategy.process(new FilterAdapter(new BandPass(3.0, 4.0)), new Wavaform());
     }
}

class Wavaform{
     private static long counter;
     private final long id = counter++;
     public String toString() { return "Wavaform "+id; }
}
class LowPass extends Filter{
     double cutoff;
     public LowPass(double cutoff) { this.cutoff = cutoff; }
     public Wavaform process(Wavaform input) { return input; }
}
class HighPass extends Filter{
     double cutoff;
     public HighPass(double cutoff) { this.cutoff = cutoff; }
     public Wavaform process(Wavaform input) { return input; }
}
class BandPass extends Filter{
     double lowCutoff, highCutoff;
     public BandPass(double lowCutoff, double highCutoff){
          this.lowCutoff = lowCutoff;
          this.highCutoff = highCutoff;
     }
     public Wavaform process(Wavaform input) { return input; }
}
//out:
//Using Processor LowPass
//Wavaform 0
//Using Processor HighPass
//Wavaform 1
//Using Processor BandPass
//Wavaform 2
{% endhighlight %}

使用接口的原因是：

- 为了能够向上转型为多个基类型，以此来获得更大的灵活性（核心原因）。
- 防止客户端程序员以此类来创建对象，并确保这仅仅是建立一个接口。

接口可以支持**多重继承**，即组合多个类的接口，从而可以实现类的扩展，同时在需要的时候又可以向上转型为每一个接口。如果要同时使用`extends`和`implements`，那必须继承在前，而且继承的父类中与接口同签名的函数可以作为对该接口的实现。接口也可以跟普通类一样通过继承和组合得到扩展，但是应该避免在需要组合的不同接口中使用相同的方法名，由于在多重继承中，覆盖、实现和重载搅在一起，而且重载方法仅通过返回类型时区分不开的，这会造成代码可读性的混乱。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;

interface CanFight { void fight(); }
interface CanSwim { void swim(); }
interface CanFly { void fly(); }
class ActionCharacter { public void fight() {}; }
class Hero extends ActionCharacter implements CanFight, CanFly, CanSwim {
     public void swim() {};
     public void fly() {};
}
public class Adventure{
     public static void cft(CanFight x){ x.fight(); }
     public static void csm(CanSwim x){ x.swim(); }
     public static void cfy(CanFly x){ x.fly(); }
     public static void acr(ActionCharacter x){ x.fight(); }
     public static void main(String[] args){
          Hero hero = new Hero();
          cft(hero); //as a CanFight
          csm(hero); //as a CanSwim
          cfy(hero); //as a CanFly
          acr(hero); //as an ActionCharacter
     }
}
{% endhighlight %}

接口最吸引人的原因是它允许一个接口具有多个不同的具体实现，因此接口常用于策略模式，这样只要实现指定的接口，就可以用任何实现该接口的对象来调用该接口规定的方法。再结合适配器，我们可以在任何类之上添加新的接口，让方法接受接口类型，然后任何实现该接口的类都可以对接口的方法进行适配。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
import java.nio.*;

class RandomDoubles {
     private static Random rand = new Random(47);
     public double next() { return rand.nextDouble(); }
}
public class AdaptedRandomDoubles extends RandomDoubles implements Readable{
     private int count;
     public AdaptedRandomDoubles(int count) { this.count = count; }
     public int read(CharBuffer cb){
          if(count-- == 0) return -1;
          String result = Double.toString(next())+" ";
          cb.append(result);
          return result.length();
     }
     public static void main(String[] args){
          Scanner sn = new Scanner(new AdaptedRandomDoubles(7));
          while(sn.hasNextDouble()) println(sn.nextDouble());
     }
}
{% endhighlight %}

接口可以被嵌套在类或者其他接口中，当实现某个接口时不需要实现嵌套在其内部的任何接口，而且private接口不能在定义它的类之外被实现，private接口强制接口中的方法不允许添加任何类型信息，即不能向上转型，嵌套的public接口可以在外部被实现，实现格式为` ... implements ClassA.NestedB `。

接口是实现多重继承的途径，而生成遵循某个接口的对象的典型方式就是**工厂方法模式**。这与直接构造不同，我们在工厂对象上调用的是创建方法，工厂对象将生成接口的某个实现对象。理论上，通过这种方式，我们的代码将完全与接口的实现分离。

##第10章 内部类

**内部类**就是将一个类的定义放在另一个类的内部，内部类有一些非常有用的特性，而且这与组合是完全不同的概念，它允许你将一些逻辑相关的类组织在一起，并且控制内部类的可视性。

在外部类中创建内部类的对象，必须明确指明对象的类型：`OuterClassName.InnerClassName`。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
public class Parcel{
     class Contents{
          private int i = 11;
          public int value() { return i; }
     }
     class Destination{
          private String label;
          Destination(String whereTo) { label = whereTo; }
          String readLabel() { return label; }
     }
     public Destination to(String str){
          return new Destination(str);
     }
     public Contents contents(){
          return new Contents();
     }
     public void ship(String dest){
          Contents con = contents();
          Destination des = to(dest);
          println(des.readLabel());
     }
     public static void main(String[] args){
          Parcel p = new Parcel();
          p.ship("Tasmania");
          Parcel q = new Parcel();
          Parcel.Contents pc = q.contents();
          Parcel.Destination pd = q.to("Borneo");
     }
}
{% endhighlight %}

###迭代器模式

内部类不光只是一种名字隐藏和组织代码的模式，内部类还具有其外围类的所有元素的访问权，更重要的内部类的对象与制造它的外围对象之间就有了一种联系，使得它能访问其外围对象的所有成员。这里有一个**迭代器模式**内部类实现的例子。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
interface Selector{
     boolean end();
     Object current();
     void next();
}
public class Iterator{
     private Object[] items;
     private int next = 0;
     public Iterator(int size) { items = new Object[size]; }
     public void add(Object obj){
          if(next < items.length) items[next++] = obj;
     }
     private class IteratorSelector implements Selector{
          private int i = 0;
          public boolean end() { return i == items.length; }
          public Object current() { return items[i]; }
          public void next() { if(i < items.length) i++; }
     }
     public Selector selector() { return new IteratorSelector(); }
     public static void main(String[] args){
          Iterator iterator = new Iterator(10);
          for(int i=0; i<10; i++)
               iterator.add(Integer.toString(i));
          Selector selector = iterator.selector();
          while(!selector.end()){
               print(selector.current()+" ");
               selector.next();
          }
          println();
     }
}
{% endhighlight %}

###`.this`&`.new`语法

外部类的名字后紧跟圆点和this，就像`OuterClassName.this`就可以在内部类中产生一个外部类的引用，当然单独的this还是内部类自己的引用。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
public class DotThis{
     void funtion() { println("DotThis.funtion()"); }
     public class Inner{
          public DotThis outer() { return DotThis.this; }
     }
     public static class StaticInner{
          public void say() { println("I'm static"); }
     }
     public Inner inner() { return new Inner(); }
     public static void main(String[] args){
          DotThis dt = new DotThis();
          DotThis.Inner dti = dt.inner();
          DotThis.Inner dni = dt.new Inner();
          //StaticInner dsi = new StaticInner();
          //两种方式都是可行的
          DotThis.StaticInner dsi = new DotThis.StaticInner();
          dti.outer().funtion();
          dni.outer().funtion();
          dsi.say();
     }
}
{% endhighlight %}

上例展示了两种创建内部类对象的方法，第二种用到了`.new`语法，就是你必须使用外部类的对象来创建内部类的对象。这是因为内部类的对象必须依附于外部类的对象，但是如果是**嵌套类**（即静态内部类），就不需要对外部类对象的引用。

内部类实现接口可以很方便的隐藏实现细节，private的内部类由于外界的访问是受限的，这样就能完全阻止任何依赖于类型的编码，因为客户端程序员只能看到原始的基类或者接口，看不到具体的实现，甚至导出类的具体类型。此外，由于从客户端程序员来看，由于不能访问新增的，不属于原接口的内容，所以扩展接口是没有意义的。

实际上可以在一个方法或者任意的作用域内定义内部类，这样做有两个好处：

- 实现某些类的接口，于是可以创建并返回对其的引用。
- 要解决一个复杂的问题，需要一个类来辅助你的解决方案，但是又不希望它是公共可用的，或者它在别的地方根本没用。

###匿名内部类

**匿名内部类**允许创建一个继承自其它基类或者接口的匿名类的对象，即在new表达式中返回一个导出类的定义，而实际上返回一个自动向上转型为基类的引用。同时，还可以使用默认或者有参的构造器，只需要传递合适的参数给基类的构造器就可以了，如果内部类中需要使用外部的对象，那么作为参数传递时必须是final类型的，否则编译错误，但若只是传递给基类的构造器，而匿名类中没有直接 使用，则不要求变量一定是final型的。匿名类中不可能有命名的构造器，所有要想实现一些构造器的行为，则需要通过**实例初始化**。实例初始化的实际效果就是构造器，但是它受到了限制，不能重载实例化方法，因为你只有这一个构造器。

匿名内部类与正规的继承相比有些限制，匿名内部类可以扩展类，也可以实现接口，但是不能两者兼备，而且也只能实现一个接口。

###工厂方法的内部类实现

工厂方法模式可以使用内部类更好的实现，服务实现的构造器可以使是private，并且也没有必要创建作为工厂的具名类（named class），而且一般只需要单一的工厂对象，因此声明为static域。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;

interface Service{
     void method1();
     void method2();
}
interface ServiceFactory{
     Service getService();
}
class Implementation1 implements Service{
     private Implementation1() {}
     public void method1() { println("Implementation1 method1()"); }
     public void method2() { println("Implementation1 method2()"); }
     public static ServiceFactory factory =
     new ServiceFactory(){
          public Service getService(){
               return new Implementation1();
          }
     };
}
class Implementation2 implements Service{
     private Implementation2() {}
     public void method1() { println("Implementation2 method1()"); }
     public void method2() { println("Implementation2 method2()"); }
     public static ServiceFactory factory =
     new ServiceFactory(){
          public Service getService(){
               return new Implementation2();
          }
     };
}
public class Factories{
     public static void serviceConsumer(ServiceFactory fact){
          Service sv = fact.getService();
          sv.method1();
          sv.method2();
     }
     public static void main(String[] args){
          serviceConsumer(Implementation1.factory);
          serviceConsumer(Implementation2.factory);
     }
}
{% endhighlight %}

###嵌套类

如果不需要内部类对象与其外部类之间有联系，可以声明为static，即**嵌套类**，他与普通嵌套类的区别是，普通的内部类对象隐式的保存了一个指向创建它的外部类对象的引用。而在嵌套类中这个引用就不存在了，这意味着：

- 要创建嵌套类的对象，并不需要其外部类的对象。
- 不能从嵌套类的对象中访问非静态的外围类对象。
- 普通的内部类不能有static方法和字段，也不能包含嵌套类，嵌套类则可以。

正常情况下，不能在接口内部放置任何代码，但嵌套类可以作为接口的一部分，接口中的类隐式的声明为public和static，这种方法可以用于测试，当然也不限于接口，嵌套类可以生成一个独立的类，运行这个类，只需要用`$`将外部类和内部类隔开（一些环境下`$`可能需要转义），执行起来就像这样`java OuterClassName\$InnerStaticClassName`。
{% highlight java linenos %}
public interface ClassInInterface{
     void funtion();
     class InnerClass implements ClassInInterface{
          public void funtion() { println("InnerClass.funtion()"); }
          public static void main(String[] args){
               new InnerClass().funtion();
          }
     }
}
{% endhighlight %}

###内部类的多层嵌套

一个内部类可以嵌套多层，而且它能透明的访问它的所有外部类的所有成员。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
public class NestedClass{
     private void className() { println(this.getClass().getSimpleName()); }
     class A{
          private void classA() { println(this.getClass().getSimpleName()); }
          class B{
               private void classB() { println(this.getClass().getSimpleName()); }
               class C{
                    void classC(){
                         className();
                         classA();
                         classB();
                         println(this.getClass().getSimpleName());
                         //NestedClass.this.className();
                         //A.this.classA();
                         //B.this.classB();
                         //println(this.getClass().getSimpleName());
                    }
               }
          }
     }
     public static void main(String[] args){
          NestedClass nc = new NestedClass();
          NestedClass.A ca = nc.new A();
          NestedClass.A.B cb = ca.new B();
          NestedClass.A.B.C cc = cb.new C();
          nc.className();
          ca.classA();
          cb.classB();
          cc.classC();
          NestedClass.A.B.C nabc = new NestedClass().new A().new B().new C();
          //new NestedClass().new A().new B().new C().classC();
     }
}
{% endhighlight %}

###使用内部类的原因

使用内部类最重要的原因是：每个内部类都能独立地继承自一个（接口的）实现，所以无论其外部类是否已经继承了某个（接口的）实现，对于内部类来说都没有影响。

内部类使得多重继承的解决方案变得完整，接口只解决了部分问题，内部类允许继承多个非接口类型（类或者抽象类）。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;

interface InterA {}
interface InterB {}
class ClassC {}
abstract class ClassD {}
class X implements InterA, InterB {}
class Y implements InterA {
     InterB makeB(){
          return new InterB() {};
     }
}
class Z extends ClassD {
     InterA makeA(){
          return new InterA() {};
     }
     InterB makeB(){
          return new InterB() {};
     }
     ClassC makeC(){
          return new ClassC() {};
     }
}
public class MultiImpl{
     static void takeA(InterA a) {}
     static void takeB(InterB b) {}
     static void takeC(ClassC c) {}
     static void takeD(ClassD d) {}
     public static void main(String[] args){
          //interface
          X x = new X();
          Y y = new Y();
          takeA(x);
          takeA(y);
          takeB(x);
          takeB(y.makeB());
          //class
          Z z = new Z();
          takeD(z);
          takeA(z.makeA());
          takeB(z.makeB());
          takeC(z.makeC());
     }
}
{% endhighlight %}

内部类其他一些特性：

- 内部类可以有多个实例，每个实例都有自己的状态信息，并且与外围类对象的信息相互独立。
- 在单个外围类中，可以让多个内部类以不同方式实现同一个接口，或继承同一个类。
- 创建内部对象的时刻不依赖与外围类对象的创建。
- 内部类是一个独立的实体。

**闭包**是一个课调用的对象，它记录了一些信息，这些信息来自于创建它的作用域。内部类是面向对象的闭包，它不仅包含外围类对象的信息，还自动拥有一个指向外围类对象的引用，在此作用域内，内部类有操作所有成员的权限，包括private。

java没有直接提供闭包和回调的功能，但是通过内部类可以提供闭包的功能，而且是一种更安全、更灵活的解决方案。回调的价值就在于它的灵活性，即可以在运行时动态的决定需要调用什么方法。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
interface Incrementable{
     void increment();
}
class Callee1 implements Incrementable{
     private int i = 0;
     public void increment(){
          i++;
          println(i);
     }
}
class MyIncrement{
     public void increment() { println("Other operation"); }
     static void fun(MyIncrement mi) { mi.increment(); }
}
class Callee2 extends MyIncrement{
     private int i = 0;
     public void increment(){
          super.increment();
          i++;
          println(i);
     }
     private class Closure implements Incrementable{
          public void increment(){
               Callee2.this.increment();
          }
     }
     Incrementable getCallbackReference(){
          return new Closure();
     }
}
class Caller{
     private Incrementable callbackReference;
     Caller(Incrementable cbh) { callbackReference = cbh;}
     void go() { callbackReference.increment(); }
}
public class Callbacks{
     public static void main(String[] args){
          Callee1 c1 = new Callee1();
          Callee2 c2 = new Callee2();
          MyIncrement.fun(c2);
          Caller caller1 = new Caller(c1);
          Caller caller2 = new Caller(c2.getCallbackReference());
          println("===============");
          caller1.go();
          caller1.go();
          println("===============");
          caller2.go();
          caller2.go();
     }
}
{% endhighlight %}

设计模式的关键是：**使变化的事物与不变的事物分开**。

应用程序框架是一个很好地使用内部类的例子，应用程序是被设计用来解决特定问题的一个类或一组类，具体的某个应用程序总是继承一个或多个类，并覆盖某些方法，以实现个性化的定制，这也是**模板方法模式**的一个例子。模板方法包含算法的基本结构，并且会调用一个或多个可覆盖的方法，以完成算法的动作。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
abstract class Event{
     private long eventTime;
     protected final long delayTime;
     public Event(long delayTime){
          this.delayTime = delayTime;
          start();
     }
     public void start(){
          eventTime = System.nanoTime()+delayTime;
     }
     public boolean ready(){
          return System.nanoTime()>=eventTime;
     }
     public abstract void action();
}
class Controller{
     private List<Event> eventList = new ArrayList<Event>();
     public void addEvent(Event e) { eventList.add(e); }
     public void run(){
          while(eventList.size()>0)
               for(Event e: new ArrayList<Event>(eventList))
                    if(e.ready()){
                         println(e);
                         e.action();
                         eventList.remove(e);
                    }
     }
}
class GreenhouseControls extends Controller{
     private boolean light = false;
     private boolean water = false;
     private String thermostat = "Day";
     public class LightOn extends Event{
          public LightOn(long delayTime) { super(delayTime); }
          public void action(){
               //hardware control code
               //turn on the light
               light = true;
          }
          public String toString() { return "Light is on"; }
     }
     public class LightOff extends Event{
          public LightOff(long delayTime) { super(delayTime); }
          public void action(){
               //hardware control code
               //turn off the light
               light = false;
          }
          public String toString() { return "Light is off"; }
     }
     public class WaterOn extends Event{
          public WaterOn(long delayTime) { super(delayTime); }
          public void action(){
               //hardware control code
               //turn on the water
               water = true;
          }
          public String toString() { return "Water is on"; }
     }
     public class WaterOff extends Event{
          public WaterOff(long delayTime) { super(delayTime); }
          public void action(){
               //hardware control code
               //turn off the water
               water = false;
          }
          public String toString() { return "Water is off"; }
     }
     public class ThermostatNight extends Event{
          public ThermostatNight(long delayTime) { super(delayTime); }
          public void action(){
               //hardware control code
               thermostat = "Night";
          }
          public String toString() { return "Thermostat is on night setting"; }
     }
     public class ThermostatDay extends Event{
          public ThermostatDay(long delayTime) { super(delayTime); }
          public void action(){
               //hardware control code
               thermostat = "Day";
          }
          public String toString() { return "Thermostat is on day setting"; }
     }
     public class Bell extends Event{
          public Bell(long delayTime) { super(delayTime); }
          public void action(){
               addEvent(new Bell(delayTime));
          }
          public String toString() { return "Bing!"; }
     }
     public class Restart extends Event{
          private Event[] eventList;
          public Restart(long delayTime, Event[] eventList) {
               super(delayTime);
               this.eventList = eventList;
               for(Event e: eventList){
                    addEvent(e);
               }
          }
          public void action(){
               for(Event e: eventList){
                    e.start(); //Rerun each Event
                    addEvent(e);
               }
               start(); //Rerun this event
               addEvent(this);
          }
          public String toString() { return "Restarting system"; }
     }
     public static class Terminate extends Event{
          public Terminate(long delayTime) { super(delayTime); }
          public void action(){
               System.exit(0);
          }
          public String toString() { return "Terminating"; }
     }
}
public class ControlFramework{
     public static void main(String[] args){
          GreenhouseControls gc = new GreenhouseControls();
          //Configuration
          gc.addEvent(gc.new Bell(900));
          Event[] eventList = {
               gc.new ThermostatNight(0),
               gc.new LightOn(200),
               gc.new LightOff(400),
               gc.new WaterOn(600),
               gc.new WaterOff(800),
               gc.new ThermostatDay(1400)
          };
          gc.addEvent(gc.new Restart(200, eventList));
          if(args.length == 1)
               gc.addEvent(
                    new GreenhouseControls.Terminate(
                         new Integer(args[0])));
          gc.run();
     }
}
{% endhighlight %}

这个例子中还用到了**命令设计模式**，即“将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可取消的操作”。在evenList中每一个请求被封装成了对象。

###内部类的继承

内部类的构造器必须连接到指向其外围类对象的引用，要继承，指向外围类对象的引用必须被初始化，而在导出类中不存在可连接的默认对象必须使用特殊的语法`enclosingClassReference.super();`。
{% highlight java linenos %}
class WithInner{
     class Inner {}
}
public class InheritInner extends WithInner.Inner{
     InheritInner(WithInner wi) { wi.super(); }
     public static void main(String[] args){
          WithInner wi = new WithInner();
          InheritInner ii = new InheritInner(wi);
     }
}
{% endhighlight %}

继承自某个外围类时，内部类并没有发生变化，这说明内部类和它的外围类是相互独立的，各自在自己的命名空间。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
class Egg{
     private Yolk y;
     protected class Yolk{
          public Yolk() { println("Egg.Yolk()"); }
     }
     public Egg(){
          println("New Egg()");
          y = new Yolk();
     }
}
public class BigEgg extends Egg{
     public class Yolk{
          public Yolk() { println("BigEgg.Yolk()"); }
     }
     public static void main(String[] args){
          new BigEgg();
     }
}
{% endhighlight %}

###局部内部类

可以在代码块内创建内部类，即**局部内部类**。局部内部类不能有访问说明符，因为它不是外围类的一部分，但是它可以访问当前代码块内的常量，以及外围类的所有成员。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
interface Couter{
     int next();
}
public class LocalInnerClass{
     private int count = 0;
     Couter getCounterLocal(final String name){
          class LocalCounter implements Couter{
               public LocalCounter() { println("LocalCounter()"); }
               public int next() {
                    print(name);
                    return count++;
               }
          }
          return new LocalCounter();
     }
     Couter getCounterAnon(final String name){
          return new Couter(){
               { println("Counter()"); }
               public int next(){
                    print(name);
                    return count++;
               }
          };
     }
     public static void main(String[] args){
          LocalInnerClass lic = new LocalInnerClass();
          Couter
          c1 = lic.getCounterLocal("Local inner "), 
          c2 = lic.getCounterAnon("Anon inner ");
          for(int i=0; i<5; i++)
               println(c1.next()); 
          for(int i=0; i<5; i++)
               println(c2.next());
     }
}
{% endhighlight %}

这里用局部内部类和匿名内部类实现了同样的功能，局部内部类在代码块以外是不可见的，这点和匿名内部类是类似的（匿名内部类本来就没有名字），但不同的是，局部内部类可以提供一个已命名的构造器，可以重载构造器，而匿名内部类只能用实例初始化。另外，局部内部类还可以提供不止一个的构造器。

每个类都会产生一个.class文件，其中包含了创建该类的全部信息（此信息产生一个“meta-class”，叫做**class对象**），内部类生成的.class文件是以其外围类名，加上`$`，在加上内部类名而命名的，如果是匿名类名，则会以数字代替。

##第11章 持有对象

复杂的程序中往往是在程序运行时才根据知道的某些条件去创建新对象，因此需要提供某种方法来保存对象，数组是最简单的保存对象的方式，但是数组有一些限制，使得它更适合保存一些基本数据类型，在更一般的情况下，还是使用**容器类**，或者叫集合类，其中基本的类型是List、Set、Queue和Map。

容器使用泛型可以防止错误的类型放入容器中，用尖括号括起来的就是**类型参数**（可以多个），同时在元素取出时类型转换也不再是必须的了。

###容器的基本概念

java容器类的用途是“保存对象”：

- **Collection**，一个独立的序列，元素服从一条或多条规则。List必须按照插入的顺序保存元素；Set不能有重复元素；Queue按照排队规则来确定对象产生的顺序（一端插入，另一端移除）。
- *Map*，一组成对的“key-value”对象，允许使用键来查找值，可以称之为“字典”。ArrayList允许使用数字来查找对象，也被称为“关联数组”，因为它将某些对象与另外一些对象关联在一起。

创建容器很容易，如`List<Apple> list = new ArrayList<Apple>()`，这里使用了向上转型，其目的在于如果你决定修改实现，只需要在创建处修改，就像这样`List<Apple> list = new LinkedList<Apple>()`。当然如果使用了某些容器特殊的功能，就不能将他们转型为通用的接口。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
class Snow {}
class Power extends Snow {}
class Crusty extends Snow {}
class Slush extends Snow {}
class Light extends Power {}
class Heavy extends Power {}
public class SimpleCollection{
     public static void main(String[] args){
          Collection<Integer> collection = new ArrayList<Integer>(Arrays.asList(1,2,3,4,5));
          Integer[] more = {6,7,8,9,10};
          collection.addAll(Arrays.asList(more));
          Collections.addAll(collection, 11,12,13,14,15);
          Collections.addAll(collection, more);
          List<Integer> list = Arrays.asList(0,1,2,3,4);
          list.set(0,5);
          //!list.add(5); //Runtime error
          for(Integer in : collection)
               print(in+" ");
          println();

          List<Snow> snow = Arrays.asList(new Power(), new Crusty(), new Slush());
          //!List<Snow> light = Arrays.asList(new Light(), new Heavy());
          List<Snow> empty = new ArrayList<Snow>();
          Collections.addAll(empty, new Light(), new Heavy());
          List<Snow> heavy = Arrays.<Snow>asList(new Light(), new Heavy());
     }
}
{% endhighlight %}

`Collection.addAll()`只能接受另一个Collection对象作为参数，不如`Arrays.asList()`和`Collection.addAll()`灵活；`Arrays.asList()`返回的实际上是一个数组，所以在使用上会有一些限制，比如不能调整大小。`Arrays.<Snow>asList()`是一个显式的**类型参数说明**。Map只能用另外一个Map实现自动初始化。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
public class PrintContainers{
     public static Collection fill(Collection<String> collection){
          collection.add("rat");
          collection.add("cat");
          collection.add("dog");
          collection.add("dog");
          return collection;
     }
     public static Map fill(Map<String,String> map){
          map.put("rat", "Fuzzy");
          map.put("cat", "Rags");
          map.put("dog", "Bosco");
          map.put("dog", "Spot");
          return map;
     }
     public static void main(String[] args){
          println(fill(new ArrayList<String>()));
          println(fill(new LinkedList<String>()));
          println(fill(new HashSet<String>()));
          println(fill(new TreeSet<String>()));
          println(fill(new LinkedHashSet<String>()));
          println(fill(new HashMap<String, String>()));
          println(fill(new TreeMap<String, String>()));
          println(fill(new LinkedHashMap<String, String>()));
     }
}
{% endhighlight %}

Set中，HashSet获取元素的速度最快；TreeSet按照比较结果的升序排列；LinkedHashSet按照添加的顺序保存对象。HashMap、TreeMap和LinkedHashMap与此类似。

List可以保证特定的序列顺序，同时可以在中间插入和移除元素：

- ArrayList，擅长随机访问，中间插入移除速度较慢。
- LinkedList，优化了顺序访问，能以较低的代价在List中间插入移除元素，但是随机访问较慢，LinkedList实现了Queue的接口，所以能进行一些队列的操作。

###迭代器

**迭代器**（也是一种设计模式），是一个对象，它的工作是遍历并选择序列中的对象，而客户端程序员不用关心容器的具体信息。

Iterator的作用是：

- 使用`iterator()`方法返回一个Iterator，Iterator准备返回序列的第一个元素。
- 使用`next()`获得下一个元素。
- 使用`hasNext()`检查是否还有元素。
- 使用`remove()`移除新近返回的对象（在`next()`之后调用）。
{% highlight java linenos %}
List<String> list = new ArrayList<String>(Arrays.asList("rat","cat","dog"));
          Iterator<String> it = list.iterator();
          while(it.hasNext()){
               String str = it.next();
               print(str+" ");
               it.remove();
          }
          println(list.size());
{% endhighlight %}

java中有Stack，但是LinkedList同样可以实现栈的所有功能，遗憾的是java中并没有公共的Stack接口，这与其他容器比起来似乎缺少了一致性。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
public class Stack<T>{
     private LinkedList<T> storage = new LinkedList<T>();
     public void push(T v) { storage.addFirst(v); }
     public T peek() { return storage.getFirst(); }
     public T pop() { return storage.removeFirst(); }
     public boolean empty() { return storage.isEmpty(); }
     public String toString() { return storage.toString(); }
     public static void main(String[] args){
          Stack<String> stack = new Stack<String>();
          stack.push("Rat");
          println(stack.peek());
          stack.pop();
          println(stack.empty());
          java.util.Stack<String> sk = new java.util.Stack<String>();
          sk.push("Rat");
          println(sk.peek());
          sk.pop();
          println(sk.empty());
     }
}
{% endhighlight %}

Set具有和Collection完全一样的接口，只是行为不同而已（继承与多态思想的典型应用：表现的行为不同）。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
public class Statistics{
     public static void main(String[] args){
          Random rand = new Random();
          Map<Integer, Integer> m = new TreeMap<Integer, Integer>();
          for(int i=0; i<1000; i++){
               int r = rand.nextInt(20);
               Integer frq = m.get(r);
               m.put(r, frq == null ? 1:frq+1);
          }
          println(m);
     }
}
{% endhighlight %}

LinkedList具有Stack的功能，同时又实现了Queue（队列）接口。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
public class QueueDemo{
     public static void main(String[] args){
          Queue<Character> queue = new LinkedList<Character>();
          for(char c: "qwertyuiop".toCharArray())
               queue.offer(c);
          while(queue.peek() != null)
               print(queue.remove()+" ");
          println();
          PriorityQueue<String> pq = new PriorityQueue<String>(Arrays.asList("qwertyuiop".split("")));
          while(pq.peek() != null)
               print(pq.remove()+" ");
          println();
     }
}
{% endhighlight %}

任何实现了Iterable接口（包含`iterator()`方法）的类都可以用于foreach语句，数组可以用佛reach，但是不代表数组也是一个Iterable。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;

public class AdapterMethodIdiom implements Iterable<String>{
     protected String[] words = "Beautiful is better than ugly".split(" ");
     public AdapterMethodIdiom() {}
     public AdapterMethodIdiom(String[] words) { this.words = words; }
     public Iterator<String> iterator(){
          return new Iterator<String>(){
               private int index = 0;
               public boolean hasNext(){
                    return index < words.length;
               }
               public String next() { return words[index++]; }
               public void remove(){
                    throw new UnsupportedOperationException();
               }
          };
     }
     public Iterable<String> reversed(){
          return new Iterable<String>(){
               public Iterator<String> iterator(){
                    return new Iterator<String>(){
                         private int index = words.length-1;
                         public boolean hasNext(){
                              return index > -1;
                         }
                         public String next() { return words[index--]; }
                         public void remove(){
                              throw new UnsupportedOperationException();
                         }
                    };
               }
          };
     }
     public static void main(String[] args){
          for(String s:new AdapterMethodIdiom())
               print(s+" ");
          println();
          for(String s:new AdapterMethodIdiom().reversed())
               print(s+" ");
          //for(Map.Entry entry: System.getenv().entrySet()){
          //     println(entry.getKey()+": "+entry.getValue());
          //}
     }
}
{% endhighlight %}

**适配器方法惯用法**将Iterable接口用于其他接口。

##第12章 用异常处理错误

程序的编译期并不能找出所有的错误，余下的错误必须在运行期间解决，这就需要错误源能通过某种方式把错误信息传递给某个接受者，该接受者知道如何正确处理这个问题，这就是**异常处理机制**，异常处理的初衷是为了方便程序员处理错误，一些无关紧要的错误可以忽略掉。

异常处理可以把异常情形和普通问题相区分，异常最重要的方面之一就是如果发生，它们将不允许程序沿正常的路径继续走下去。所有的异常都有两个构造器：一个默认构造器，一个接受字符串作为参数，异常的根类为Throwable。

try块是一个**监控区域**，可以捕获到异常，并由经跟其后的catch字句来处理（可以有多个），每个catch字句就像一个仅接受一个特殊参数的方法，只有匹配的catch字句才能执行（匹配不是绝对的匹配，只是找出“最近”的处理程序，子类的对象也可以匹配基类的处理程序，，Exception就可能屏蔽掉所有它之后的处理程序），而如果有finally语句，它总会被执行（在有break、continue以及return时也会执行）。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;

public class MultiReturns{
     public static void fun(int i){
          println("Initialization");
          try{
               println("Point 1");if(i==1) return;
               println("Point 2");if(i==2) return;
               println("Point 3");if(i==3) return;
               println("Point 4");if(i==4) return;
               println("End"); return;
          }finally{
               println("Performing cleanup");
          }
     }
     public static void main(String[] args){
          for(int i=0; i<5; i++) fun(i);
     }
}
{% endhighlight %}

异常处理理论上有两种基本模型：java（还有C++）支持**终止模型**，这种模型假设程序无法返回到异常发生的地方继续执行，一旦异常抛出，表明错误已无法挽回；另一种是**恢复模型**，异常处理程序的工作是修正错误，然后重新尝试出问题的方法。

###创建自定义异常

自定义异常必须从已有的异常类继承，最好是选择意思相近的，还可以用`java.util.logging`工具将异常输出到日志中。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.logging.*;
import java.io.*;
class SimpleException extends Exception{
     public SimpleException() {}
     public SimpleException(String msg) { super(msg); }
}
class LoggingException extends Exception{
     private static Logger logger = Logger.getLogger("LoggingException");
     public LoggingException() {
          StringWriter trace = new StringWriter();
          printStackTrace(new PrintWriter(trace));
          logger.severe(trace.toString());
     }
}
public class MyException{
     public void fun() throws SimpleException{
          println("Throw SimpleException from fun()");
          throw new SimpleException();
     }
     public void gun() throws SimpleException{
          println("Throw SimpleException from gun()");
          throw new SimpleException("Originated in gun()");
     }
     public void hun() throws LoggingException{
          println("Throw LoggingException from hun()");
          throw new LoggingException();
     }
     public static void main(String[] args){
          MyException me = new MyException();
          try{
               me.fun();
          }catch(SimpleException se){
               println("Oops, Caught it!");
          }
          try{
               me.gun();
          }catch(SimpleException se){
               se.printStackTrace(System.out);
               //se.printStackTrace();
          }
          try{
               me.hun();
          }catch(LoggingException le){
               System.err.println("Caught "+le);
          }
     }
}
{% endhighlight %}

使用Exception可以捕获所有异常，因为它是异常的基类（跟编程相关），`printStackTrace()`提供的信息可以通过`getStackTrace()`方法直接访问，返回一个由栈轨迹中的元素组成的数组。异常可以被重新抛出（`throw e`），这样会把异常抛给上一级环境中的异常处理程序。常常需要在捕获一个异常以后抛出另外一个异常，并且希望把原始异常的信息保存下来，这可以使用**异常链**。现在所有的Throwable子类的构造器都可以接受一个cause对象（用来表示原始异常）作为参数，有三个基本的异常类提供带cause参数的构造器，即Error、Exception和RuntimeException，如果要把其他类型的异常链接起来，需要使用`initCause()`方法。

###Java标准异常

Throwable对象分为两种类型（Throwable的子类）：**Error**是编译时和系统错误；**Exception**是可以被抛出的基本类型。运行时异常（**RuntimeException**）是个特列，他们自动被JVM抛出，不用自己捕获。

java中异常的限制也具有继承性，即子类覆盖方法只能抛出在基类方法的异常说明列出的那些异常，但是异常限制对构造器不起作用，也不能基于异常说明来重载方法，因为异常说明不属于方法类型的一部分。应该时刻注意“如果异常发生了，所有的东西都能被正确清理吗”，特别是在构造器中，需要注意，一般不要用finally来清理，因为它总会执行，比较安全的做法是使用嵌套的try字句。使用通用的清理惯用法基本规则是：**在创建需要清理的对象之后，立即进入一个try-finally语句块**。

异常处理的一个重要原则是**只有在你知道如何处理的情况下才捕获异常**，异常处理的一个重要目的就是把错误处理的代码和错误发生的地点相分离，错误代码的处理可以放在另一段代码中。

java中**被检查的异常**可能会使问题变得复杂，因为它强制你在可能还没有准备好处理异常的时候被迫加上catch子句，如果不处理则会引发“吞食则有害”的问题。简单的程序可以将异常打印到控制台，但这不是通用的做法，一个比较好的办法是把**被检查的异常**转化为**不检查的异常**，即把“检查的异常”包装进`RuntimeException`，而且异常链保证你的异常不会丢失，你可以在合适的地方用`getCause()`捕获并处理特定的异常。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.logging.*;
import java.io.*;
class WrapCheckedException{
     void throwRuntimeException(int type){
          try{
               switch(type){
                    case 0: throw new FileNotFoundException();
                    case 1: throw new IOException();
                    case 2: throw new RuntimeException("Where am I");
                    default: return;
               }
          }catch(Exception e){
               throw new RuntimeException(e);
          }
     }
}
class SomeOtherException extends Exception {}
public class TurnoffChecking{
     public static void main(String[] args){
          WrapCheckedException wce = new WrapCheckedException();
          wce.throwRuntimeException(3);
          for(int i=0; i<4; i++)
               try{
                    if(i<3) wce.throwRuntimeException(i);
                    else throw new SomeOtherException();
               }catch(SomeOtherException se){
                    println("SomeOtherException: "+se);
               }catch(RuntimeException re){
                    try{
                         throw re.getCause();
                    }catch(FileNotFoundException fe){
                         println("FileNotFoundException: "+fe);
                    }catch(IOException ie){
                         println("IOException: "+ie);
                    }catch(Throwable te){
                         println("Throwable: "+te);
                    }
               }finally{
                    println("Handled all exception!");
               }
     }
}
{% endhighlight %}

异常处理指南：

1. 在适当的级别处理问题（在知道该如何处理时才捕获）。
2. 解决问题并且重新调用产生异常的方法。
3. 进行少许修补，然后绕过异常经常发生的地方继续执行。
4. 用别的数据进行计算，以代替方法预计会返回的值。
5. 把当前环境下能做的事情尽量做完，然后把相同的异常重抛到更高层。
6. 把当前环境下能做的事情尽量做完，然后把相同的异常抛到更高层。
7. 终止程序。
8. 进行简化。
9. 让类库和程序更安全。
