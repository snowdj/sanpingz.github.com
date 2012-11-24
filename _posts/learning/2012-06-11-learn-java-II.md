---
layout: post
title: "Thinking in Java (II)"
category: learning
tags: [java]
description: |
  java的经典名著，无需过多解释，没仔细研读过，别说你学过java，随手翻翻也感觉受益匪浅，java是我喜欢的语言之一，Thinking in Java也是我看过的最好的技术书籍之一。
---
{% include JB/setup %}

##第13章 字符串

###字符串基本操作

String对象是不可变的，每一个试图修改字符串的方法实际上都是生成个一个新的字符串，所以String对象是只读的。java不允许程序员重载任何的运算符，用于String的运算符只有重载过的“+”和“=+”。StringBuilder是一个更高效的处理字符串的类，在复杂的字符串操作中可以使用以提高效率。

Formatter类提供了格式化功能，格式化的格式为`%[argument_index$][flags][width][.precision]conversion`。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
import java.io.*;
import java.math.*;
class AboutString{
     public String toString(){
          return "this: "+this+"\n";
     }
     public static void main(String[] args){
          String s = "spam";
          String ss = s.toUpperCase();
          println("s: "+s+", ss: "+ss);
          println("s+ss: "+(s+ss));
          s+=ss;
          println("s: "+s+", ss: "+ss);
          StringBuilder sb = new StringBuilder("[");
          for(int i=0; i<20; i++){
               sb.append(new Random().nextInt(20));
               sb.append(", ");
          }
          sb.delete(sb.length()-2, sb.length());
          sb.append("]");
          println(sb);
          println("charAt(): "+s.charAt(1));
          println("toCharArray(): "+s.toCharArray());
          //println(new AboutString());
          int x = 5;
          double y = java.lang.Math.PI;
          String z = "Format out: ";
          System.out.println(z+"["+x+", "+y+"]");
          System.out.printf("%s[%d, %f]\n", z, x, y);
          System.out.format("%s[%d, %f]\n", z, x, y);
          PrintStream outAlias = System.out;
          Formatter ft = new Formatter(outAlias);
          ft.format("%s[%d, %f]\n", z, x, y);
          ft.format("%-10s %5s %-10s\n", "Item", "Qty", "Price");
          ft.format("%-10s %5d %-10.2f\n", "Jack", 4, 10.87545);
          ft.format("%-10s %5h %-10.4e\n", "Rose", 4231, 10875.45);
          ft.format("%-10b %5x %-10d\n", "Calvin", 42, new BigInteger("32442424042412429487"));
          try{
               StringBuilder sbr = new StringBuilder();
               int n = 0;
               File file = new File("AboutString.class").getAbsoluteFile();
               BufferedInputStream bf = new BufferedInputStream(new FileInputStream(file));
               byte[] bt = new byte[bf.available()];
               bf.read(bt);
               bf.close();
               for(byte b:bt){
                    if(n%16==0) sbr.append(String.format("%05X: ", n));
                    sbr.append(String.format("%02X ", b));
                    n++;
                    if(n%16==0) sbr.append("\n");
                    }
                    sbr.append("\n");
                    println(sbr);
               }catch(Exception e){}
          }
}
{% endhighlight %}

###正则表达式

**正则表达式**是一种强大而灵活的文本处理工具，能够处理诸如：匹配、选择、编辑以及验证等字符串相关的问题。

java中正则表达式中的反斜杠有一些不同的处理，其他语言中“\\”表示插入一个普通的反斜杠，而java中表示这就是一个正则表达式中表示特殊含义的反斜杠，要插入一个普通的反斜杠，应该是“\\\\”，但是正常的换行、制表符之类的东西还是只需要单个反斜杠`\n\t`。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
public class StringRegular{
     public static void main(String[] args){
          println("-1234".matches("-?\\d+"));
          println("1234".matches("-?\\d+"));
          println("+1234".matches("-?\\d+"));
          println("+1234".matches("(-|\\+)?\\d+"));
          println(Arrays.toString("I'm CEO, bitch!".split("\\W+")));
          println("I'm CEO, bitch!".replaceAll("b\\w+", "boy"));
     }
}
{% endhighlight %}

###创建正则表达式

**量词**描述了一个模式吸收输入文本的方式：

- 贪婪型，为所有可能的模式搜索尽可能多的匹配项。
- 勉强型，用问号指定，匹配满足模式所需的最少字符数。
- 占有型，java专有，以加号来指定，更高级，常用来防止正则表达式失控。

String类对正则表达式的支持有限，java更强大的正则表达式支持需要`java.util.regex`包，产生的Pattern对象和Matcher对象可以实现很多功能。最简单的使用方法是先调用静态的`Pattern.complie()`编译你的正则表达式，然后在使用Pattern对象的`matcher()`方法来匹配你的输入，还可以使用`split()`方法将输入字符串根据正则表达式断开成字符串数组。Pattern类的`compile()`方法还可以接受一个标记参数，以调整匹配的行为，可以使用`|`组合多个。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.regex.*;
import java.util.*;
public class RegularExpression{
     public static void main(String[] args){
          String regex = "\\w+";
          String str = "There should be one-- and preferably only one --obvious way to do it.";
          //Pattern p = Pattern.compile(regex);
          //Matcher m = p.matcher(str);
          Matcher m = Pattern.compile(regex).matcher(str);
          while(m.find()){
               println("Match "+m.group()+" at "+m.start()+"-"+(m.end()-1));
          }
          println(Arrays.toString(Pattern.compile("\\-\\-").split(str,3)));
     }
}
{% endhighlight %}

###使用正则表达式进行扫扫描

可以使用Scanner类或者正则表达式进行文件，或者字符串扫描，以及分词等操作，StringTokenizer也能进行分词，但基本上被淘汰了。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
import java.util.regex.*;
import java.io.*;
public class SimpleRead{
     public static BufferedReader input = new BufferedReader(
          new StringReader("Sir Robin of Camelot\n22 1.61803"));
     public static void main(String[] args){
          try{
               println("What's your name?");
               String name = input.readLine();
               println(name);
               println("How old of you, and what's your favorite double?");
               String numbers = input.readLine();
               println(numbers);
          }catch(IOException ie){
               println("IOException");
          }
          String data =
          "Calvin\n"+
          "zhangsp@163.com\n"+
          "24\n"+
          "12619242230";
          Scanner scanner = new Scanner(data);
          String reg = "^[a-zA-Z][\\w\\.-]*[a-zA-Z0-9]@[a-zA-Z0-9][\\w\\.-]*[a-zA-Z0-9]\\.[a-zA-Z][a-zA-Z\\.]*[a-zA-Z]$";
          Matcher m = Pattern.compile(reg).matcher("");
          while(scanner.hasNext()){
               String line = scanner.nextLine();
               m.reset(line);
               print(line+" ");
               try{
                    print("e-mail: "+m.group(1)+" ");
               }catch(IllegalStateException ie){
                    println(ie.getMessage());
               }
          }
          println();
          reg="\\d*";
          String age=null;
          String phone=null;
          while(scanner.hasNext(reg)){
               scanner.next(reg);
               MatchResult match = scanner.match();
               age = match.group(1);
               phone = match.group(2);
          }
          println("age: "+age);
          println("phone: "+phone);
          //scanner = new Scanner("Calvin, 24, zhangsp@163.com, 13619242230");
          scanner = new Scanner("13, 24, 163, 136");
          println(scanner.delimiter());
          scanner.useDelimiter("\\s*,\\s*");
          while(scanner.hasNextInt()){
               //println("age: "+scanner.nextInt());
               //println("phone: "+scanner.nextInt());
               println(scanner.nextInt());
          }
         
     }
}
{% endhighlight %}

##第14章 类型信息

**运行时类型信息（RTTI）**使得你可以在程序运行时发现和使用类型信息，java中在运行时识别对象和类型信息有两种方式，即传统的RTTI（假定我们在编译时已经知道了所有的类型）和**反射**机制（允许我们在运行时发现和使用类的信息）。

面向对象编程的基本目的是：**让代码只操纵对基类的引用**，这样就很容易扩展新的类，而不会影响的原来的代码。

在java中，所有的类型转换都是在运行时进行类型正确性检查的。容器中的对象是以Object对象来持有的，在运行时由RTTI类型转换操作转换为声明的类型参数类型，在编译时由容器和java的泛型系统来确保这一点，而具体的类型转换由多态机制来实现。

**Class对象**包含了与类有关的信息，用来创建类的所有常规对象，每个类都有一个Class对象，java就是使用Class对象来执行RTTI的，而所有的Class对象都属于同一个Class类。所有的类都是在第一次使用时动态加载到JVM中的，使用new操作符创建的新对象，实际上是对类的静态成员的引用，这是因为它调用了静态的构造器。
{% highlight java linenos %}
package typeinfo.demo;
import static com.mceiba.util.Print.*;

interface HasBatteries {}
interface Waterproof {}
interface Shoots {}
class Toy{
     Toy() {}
     Toy(int i) {}
}
class FancyToy extends Toy implements HasBatteries, Waterproof, Shoots{
     FancyToy() { super(1); }
}
public class ClassDemo{
     static void printInfo(Class cc){
          println("Class name: "+cc.getName()+" is interface ? ["+cc.isInterface()+"]");
          println("Simple name: "+cc.getSimpleName());
          println("Canonical name: "+cc.getCanonicalName());
     }
     public static void main(String[] args){
          Class c = null;
          try{
               c = Class.forName("typeinfo.demo.FancyToy");
          }catch(ClassNotFoundException e){
               println("Can't find FancyToy");
               System.exit(1);
          }
          printInfo(c);
          for(Class face: c.getInterfaces()) printInfo(face);
          Class up = c.getSuperclass();
          Object obj = null;
          try{
               obj = up.newInstance();
          }catch(InstantiationException e){
               println("Cannot instantiate");
               System.exit(1);
          }catch(IllegalAccessException e){
               println("Cannot access");
               System.exit(1);
          }
          printInfo(obj.getClass());
     }
}
{% endhighlight %}

除了使用`Class.forName()`，`getClass()`，还可以使用**类字面常量**来生成对Class对象的引用，调用格式为`[className].class`，适用于普通类、接口、数组以及基本数据类型（**字面量**是在源代码级别表示一个对象或者数值的字符串）。对于基本类型的包装类还有一个**TYPE**字段，也是一个引用，指向对应的基本类型的Class对象，如`Boolean.TYPE`等价于`boolean.class`。它更简单、更安全，因为它在编译时就检查错误（不用放在try字句中），而且根除了`forName()`方法的调用，所以更高效。

java中为了使用类而进行的准备工作包括三个步骤：

1. 加载，由类加载器执行，查找字节码（通常在classpath指定的路径中查找），并从字节码中创建一个Class对象。
2. 链接，验证类中的字节码，为静态域分配存储空间，必要时解析这个类创建的对其他类的所有引用。
3. 初始化，如果该类具有超类，则对其初始化，执行静态初始化器和静态初始化块。

初始化被延迟到了对静态方法或者非常数静态域进行首次引用时才执行，而使用`.class`创建对Class对象的引用时不会自动初始化该Class对象。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
class Initable1{
     static final int staticFinal1 = 147;
     static final int staticFinal2 = ClassInitialization.rand.nextInt(1000);
     static { println("Initializing Initable1"); }
}
class Initable2{
     static int staticNonFinal = 247;
     static { println("Initializing Initable2"); }
}
class Initable3{
     static int staticNonFinal = 347;
     static { println("Initializing Initable3"); }
}
public class ClassInitialization{
     public static Random rand  =new Random();
     public static void main(String[] args) throws Exception{
          Class initable1 = Initable1.class;
          println("After creating Initable1 ref");
          println(Initable1.staticFinal1);
          println(Initable1.staticFinal2);
          println(Initable2.staticNonFinal);
          Class initable3 = Class.forName("Initable3");
          println("After creating Initable3 ref");
          println(Initable3.staticNonFinal);
     }
}
{% endhighlight %}

仅使用`.class`来获取对象的引用不会发生初始化，而`forName()`就会立即进入初始化。static final的“编译期常量”不需要对类进行初始化就可以读取，但是对static final的非编译期常量的调用就会引发强制的初始化。只是static而不是final型的域在读取之前要进行链接和初始化。

Class引用可以指向某个Class对象，普通的类指向可以被重新指向任何其他的Class对象，因为它就如同一个基类的对象，可以将任何的子类对象转型，这样有时是有害的，因为它不会产生编译器的警告信息（本来就是合法的），但是可以使用泛型的语法来避免这一点，而且可以使用`?`通配符，以及通配符跟extends的组合来限定自己或者子类的类型。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
class CountedInteger{
     private static long counter;
     private final long id = counter++;
     //public CountedInteger(int id) {};
     public String toString() { return Long.toString(id); }
}
class FilledList<T>{
     private Class<T> type;
     public FilledList(Class<T> type) { this.type = type; }
     public List<T> create(int nElements){
          List<T> result = new ArrayList<T>();
          try{
               for(int i=0; i<nElements; i++)
                    result.add(type.newInstance());
          }catch(Exception e){
               throw new RuntimeException(e);
          }
          return result;
     }
}
public class GenericClassRef{
     public static void main(String[] args) throws Exception{
          Class intClass = int.class;
          Class<Integer> genericIntClass = int.class;
          genericIntClass = Integer.class;
          intClass = double.class;
          //!genericIntClass = double.class;
          Class<?> comIntClass = int.class;
          comIntClass = double.class;
          //!Class<Number> numClass = int.class;
          Class<? extends Number> subNumClass = int.class;
          subNumClass = int.class;
          subNumClass = double.class;
          subNumClass = Number.class;
          Class<? super Number> superIntegerClass = Number.class;
          println(superIntegerClass.getName());
          CountedInteger ins = CountedInteger.class.newInstance();
          //Integer inte = Integer.class.newInstance();

          FilledList<CountedInteger> fl = new FilledList<CountedInteger>(CountedInteger.class);
          println(fl.create(15));
     }
}
{% endhighlight %}

`newInstance()`可以产生一个对象实例，效果如同不带参数列表的new表达式创建一个实例，但前提是必须有一个默认的无参构造函数，因为
`newInstance()`不接收任何参数，此外`newInstance()`在下列情况会抛出异常：

- IllegalAccessException - 如果此类或其无参构造方法是不可访问的。
- InstantiationException - 如果此 Class 表示一个抽象类、接口、数组类、基本类型或 void； 或者该类没有无参构造方法； 或者由于某种其他原因导致实例化过程失败。 
- ExceptionInInitializerError - 如果该方法引发的初始化失败。
- SecurityException - 如果存在安全管理器 s，并满足下列任一条件：
     1. 调用 s.checkMemberAccess(this, Member.PUBLIC) 拒绝创建该类的新实例 。
     2. 调用方的类加载器不同于也不是该类的类加载器的一个祖先，并且对 s.checkPackageAccess() 的调用拒绝访问该类的包 。

`cast()`方法接受参数对象，并将其转型为Class引用的类型，效果类似于向下转型。子类声明为父类的引用时，在编译期间，编译器只把它当做父类来处理，如果需要子类额外的功能，则需要显示的向下转型，使用`instanceof()`可以判断对象是不是某个特定类型的实例，验证成功，则说明可以进行转型，与它等价的是`Class.isInstance()`，它，它们都能判断是否是本类或者派生类，而如果用`==`或者`equals()`判断类对象，只能判断是不是本类。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
class Building {}
class House extends Building {}
public class ClassCasts{
     public static void main(String[] args) throws Exception{
          Building b = new House();
          Class<House> house = House.class;
          House h = house.cast(b);
          //h = (House)b; //the same result
          println(h instanceof Building);
          println(house.isInstance(h));
          //!println(h instanceof House.class);
     }
}
{% endhighlight %}

###反射机制

**反射**提供了一种机制，用来检查可用的方法，并返回方法名。反射与RTTI的区别在于：对RTTI来说，编译器在编译时打开和检查`.class`文件，而对反射机制来说，`.class`文件在编译时是不可取的，所以在运行时打开和检查`.class`文件。

RTTI可以提供在编译时已知的类型信息，但是人们要在运行时获得类信息的另一个动机是，希望能够提供在跨网络的远程平台上创建和运行对象的能力，即远程方法调用（RMI），它允许一个java程序将对象分布在多台电脑上，即分布式的能力，这就需要反射机制的协助。

通常不需要直接使用反射机制，但是需要创建更动态的代码时反射就会很有用，所以大多数情况下用来支持其他特性，如对象序列化和JavaBean，下面是一个类方法提取器的例子。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.regex.*;
import java.lang.reflect.*;
public class ShowMethods{
     private static String usage =
          "usage:\n"+
          "ShowMethods qualified.class.name\n"+
          "To show all methods in class or:\n"+
          "ShowMethods qualified.class.name word\n"+
          "To search for methods involving 'word'";
     private static Pattern p =Pattern.compile("\\w+\\.");
     public static void main(String[] args) throws Exception{
          if(args.length<1){
               println(usage);
               System.exit(0);
          }
          int lines = 0;
          try{
               Class<?> c = Class.forName(args[0]);
               Method[] methods = c.getMethods();
               Constructor[] ctors = c.getConstructors();
               if(args.length==1){
                    for(Method method : methods)
                         println(p.matcher(method.toString()).replaceAll(""));
                    for(Constructor ctor : ctors)
                         println(p.matcher(ctor.toString()).replaceAll(""));
                    lines = methods.length+ctors.length;
               }else{
                    for(Method method : methods)
                         if(method.toString().indexOf(args[1])!=-1){
                              println(p.matcher(method.toString()).replaceAll(""));
                              lines++;
                         }
                    for(Constructor ctor : ctors)
                         if(ctor.toString().indexOf(args[1])!=-1){
                              println(p.matcher(ctor.toString()).replaceAll(""));
                              lines++;
                         }
               }
          }catch(ClassNotFoundException e){
               println(e.getMessage()+": "+e);
          }
     }
}
{% endhighlight %}

###动态代理

**代理模式**是为了提供额外的或不同的操作，而插入的用来代替“实际”对象的对象，这些操作设计和“实际”对象之间的通信，所以代理充当的是一个中间人的角色。设计模式的关键是**封装修改**，代理模式也是为了把额外的操作分离出来并封装起来，以易于修改。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;

interface Interface{
     void doSomething();
     void somethingElse(String arg);
}
class RealObject implements Interface{
     public void doSomething() { println("doSomething"); }
     public void somethingElse(String arg) { println("somethingElse "+arg); }
}
class SimpleProxy implements Interface{
     private Interface proxied;
     public SimpleProxy(Interface proxied) { this.proxied = proxied; }
     public void doSomething(){
          println("SimpleProxy doSomething");
          proxied.doSomething();
     }
     public void somethingElse(String arg) {
          println("SimpleProxy somethingElse "+arg);
          proxied.somethingElse(arg);
     }
}
public class Proxy{
     public static void consumer(Interface iface){
          iface.doSomething();
          iface.somethingElse("bonn");
     }
     public static void main(String[] args){
          consumer(new RealObject());
          consumer(new SimpleProxy(new RealObject()));
     }
}
{% endhighlight %}

**动态代理**可以更好的解决问题，它可以动态的创建代理并动态的处理对所代理方法的调用。在动态代理上所做的所有调用都会被重定向到单一的调用处理器上，它的工作是揭示调用的类型并确定相应的对策。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.lang.reflect.*;
import java.lang.reflect.Proxy;

interface Something{
     void doSomething();
     void somethingElse(String arg);
}
class RealObjects implements Something{
     public void doSomething() { println("doSomething"); }
     public void somethingElse(String arg) { println("somethingElse "+arg); }
}
class DynamicProxyHandler implements InvocationHandler{
     private Object proxied;
     public DynamicProxyHandler(Object proxied) { this.proxied = proxied; }
     public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
          println("**** proxy: "+proxy.getClass()+", method: "+method+", args: "+args);
          if(args != null)
               for(Object arg : args) println("  "+arg);
          return method.invoke(proxied, args);
     }
}
public class DynamicProxy{
     public static void consumer(Something iface){
          iface.doSomething();
          iface.somethingElse("bonn");
     }
     public static void main(String[] args){
          RealObjects real = new RealObjects();
          consumer(real);
          Something proxy = (Something)Proxy.newProxyInstance(
               Something.class.getClassLoader(),
               new Class[]{ Something.class },
               new DynamicProxyHandler(real));
          consumer(proxy);
     }
}
{% endhighlight %}

使用静态方法`Proxy.newProxyInstance()`可以创建动态代理，这个方法需要一个类加载器（通常可以从已加载的对象中获取其类加载器），一个需要代理实现的接口列表（不是类或抽象类），以及`InvocationHandler`接口的一个实现。动态代理可以将所有调用重定向到调用处理器，因此通常会向调用处理器的构造器传递一个实际对象的引用，从而使得调用处理器在执行某个中介任务时，可以将请求转发。`invoke()`方法中传递进来了代理对象，以防止你需要区分请求的来源，要注意在代理上方法的调用，因为对接口的调用将被重定向为对代理的调用。通常，先执行被代理的操作，然后使用`Method.invoke()`将请求转发给被代理对象，并传入必需的参数，这个地方可以传递其他的参数来过滤掉某些方法的调用。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.lang.reflect.*;
import java.lang.reflect.Proxy;

interface SomeMethods{
     void boring1();
     void boring2();
     void boring3();
     void interesting(String arg);
}
class Implementation implements SomeMethods{
     public void boring1() { println("boring1"); }
     public void boring2() { println("boring2"); }
     public void boring3() { println("boring3"); }
     public void interesting(String arg) { println("interesting "+arg); }
}
class MethodSelector implements InvocationHandler{
     private Object proxied;
     public MethodSelector(Object proxied) { this.proxied = proxied; }
     public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
          if(method.getName().equals("interesting"))
               println("Proxy detected the interesting method");
          return method.invoke(proxied, args);
     }
}
public class SelectingMethods{
     public static void main(String[] args){
          SomeMethods proxy = (SomeMethods)Proxy.newProxyInstance(
               SomeMethods.class.getClassLoader(),
               new Class[]{ SomeMethods.class },
               new MethodSelector(new Implementation()));
          proxy.boring1();
          proxy.boring2();
          proxy.boring3();
          proxy.interesting("bonn");
     }
}
{% endhighlight %}

对空对象方法的调用会产生烦人的`NullPointerException`异常，但是它并非都是有害的，空对象最有用之处在于它更靠近数据，因为对象表示的是问题空间内的实体。空对象都是单例，可以在合适的时候被其他非空对象替代，或者传递类型信息。

###接口与类型信息

interface关键字的一种重要目标就是允许程序员隔离构件，进而降低耦合性。但是通过类型信息，这种耦合性还是会传播出去，我们不想让客户端程序员访问的方法或域，通过反射还是可以访问，甚至修改，private、私有内部类、匿名类都是可以访问的。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.lang.reflect.*;

interface A{
     void fun();
}
class B implements A{
     public void fun() { println("public B.fun()"); }
     public void gun() { println("public B.gun()"); }
}
class C implements A{
     private int i = 3;
     private final String str = "private final C.str";
     private static final String sfs = "private static final C.str";
     public void fun() { println("public C.fun()"); }
     public void gun() { println("public C.gun()"); }
     void hun() { println("package C.hun()"); };
     protected void jun() { println("protected C.jun()"); }
     private void kun() { println("private C.kun()"); }
     private static class InnerA implements A{
          public void fun() { println("public InnerA.fun()"); }
          public void gun() { println("public InnerA.gun()"); }
          void hun() { println("package InnerA.hun()"); };
          protected void jun() { println("protected InnerA.jun()"); }
          private void kun() { println("private InnerA.kun()"); }
     }
     public static A makeInnerA() { return new InnerA(); }
     public static A makeAnonA(){
          return new A(){
               public void fun() { println("public AnonA.fun()"); }
               public void gun() { println("public AnonA.gun()"); }
               void hun() { println("package AnonA.hun()"); }
               protected void jun() { println("protected AnonA.jun()"); }
               private void kun() { println("private AnonA.kun()"); }
          };
     }
}
public class HiddenImplementation{
     static void callHiddenMethod(Object a, String methodName) throws Exception{
          Method method = a.getClass().getDeclaredMethod(methodName);
          method.setAccessible(true);
          method.invoke(a);
     }
     static Object setHiddenField(Object a, Object value, String fieldName) throws Exception{
          Field f = a.getClass().getDeclaredField(fieldName);
          f.setAccessible(true);
          f.set(a, value);
          return f.get(a);
     }
     static Object getHiddenField(Object a, String fieldName) throws Exception{
          Field f = a.getClass().getDeclaredField(fieldName);
          f.setAccessible(true);
          return f.get(a);
     }
     public static void main(String[] args) throws Exception{
          A a = new C();
          a.fun();
          //!a.gun();
          println(a instanceof A);
          println(a instanceof B);
          println(a instanceof C);
          println(a.getClass().getName());
          C c = (C)a;
          c.gun();
          //!c.str;
          println(getHiddenField(c, "i"));
          setHiddenField(c, 33, "i");
          println(getHiddenField(c, "i"));
          println(getHiddenField(c, "str"));
          setHiddenField(c, "final can be changed", "str");
          println(getHiddenField(c, "str"));
          println(getHiddenField(c, "sfs"));
          //!setHiddenField(c, "static final can be changed", "sfs");
          //println(getHiddenField(c, "sfs"));

          C cc = new C();
          println(getHiddenField(cc, "i"));
          println(getHiddenField(cc, "str"));
          println(getHiddenField(cc, "sfs"));


          c.hun();
          c.jun();
          //!c.kun();
          callHiddenMethod(c, "kun");
          A in = C.makeInnerA();
          callHiddenMethod(in, "fun");
          callHiddenMethod(in, "gun");
          callHiddenMethod(in, "hun");
          callHiddenMethod(in, "kun");

          A an = C.makeInnerA();
          callHiddenMethod(an, "fun");
          callHiddenMethod(an, "gun");
          callHiddenMethod(an, "hun");
          callHiddenMethod(an, "kun");
     }
}
{% endhighlight %}

其中的修改都只是针对于对象的，static的域是不能被修改的，因为它是和类相关联的。通常这些违反访问权限的操作并非都是最糟糕的事情，这种类中留下的后门实际上是可以解决某些特定类型的问题，当然访问权限最主要的作用还是在于封装性，让合适的人看到合适的域或者方法，所以违反访问权限的做法不见得是有意义的，相反却可以解决一些特定的问题。

##第15章 泛型

###泛型类

**泛型**实现了**参数化类型**的概念，使代码可以应用于多种类型，这是通过解耦类或者方法使用类型之间的约束，编译器会为你转换类型。

泛型主要目的之一就是用来指定容器要持有什么样类型的对象，而且由编译器来保证类型的正确性。类型参数不能使用基本类型，但是自动包装功能依然能使我们在内部使用基本类型。

**生成器（generator）**是一种专门负责生成创建对象的类，是工厂模式的一种应用，只不过它不需要参数，只有一个产生新对象的方法`next()`。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
interface Generator<T> { T next(); }
class Coffee{
     private static long counter;
     private final long id = counter++;
     public String toString() { return getClass().getSimpleName()+" "+id; }
}
class Latte extends Coffee {}
class Mocha extends Coffee {}
class Cappuccino extends Coffee {}
class Americano extends Coffee {}
class Breve extends Coffee {}
public class CoffeeGenerator implements Generator<Coffee>, Iterable<Coffee>{
     private Class[] types = { Latte.class, Mocha.class, Cappuccino.class, Americano.class, Breve.class};
     private static Random rand = new Random(47);
     private int size = 0;
     public CoffeeGenerator() {};
     public CoffeeGenerator(int sz) { size = sz; };
     public Coffee next(){
          try{
               return (Coffee)types[rand.nextInt(types.length)].newInstance();
          }catch(Exception e){
               throw new RuntimeException(e);
          }
     }
     class CoffeeIterator implements Iterator<Coffee>{
          int count = size;
          public boolean hasNext() { return count>0; }
          public Coffee next() {
               count--;
               return CoffeeGenerator.this.next();
          }
          public void remove() {
               throw new UnsupportedOperationException();
          }
     }
     public Iterator<Coffee> iterator() { return new CoffeeIterator(); }
     public static void main(String[] args){
          CoffeeGenerator gen = new CoffeeGenerator();
          for(int i=0; i<5; i++) {
               println(gen.next());
          }
          for(Coffee c: new CoffeeGenerator(5)) {
               println(c);
          }
     }
}
{% endhighlight %}

###泛型方法

**泛型方法**能够独立于类而产生变化，基本的原则是尽量的使用泛型方法。static方法无法使用泛型类的类型参数，所以只能使用泛型方法，泛型方法的参数列表置于返回值之前。泛型类使用前需指明具体的参数，而泛型方法则无需指明，编译器会自动执行**来类型参数推断**，所以可以直接使用，就如同被无限次的重载过，但是类型推断只对赋值操作有效，其他时候并不起作用，不过可以使用显示的类型说明来解决，即在点操作符与方法名之间插入带有类型参数的尖括号，在内部类中可能需要在点操作符前加this，而静态方法点操作符前需要类名。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import static com.mceiba.util.Container.*;
import java.util.*;

public class GenericMethods{
     public <T> void fun(T t){
          println(t.getClass().getName()+", "+t);
     }
     public static <T> void gun(T t){
          println("static, "+t.getClass().getName()+", "+t);
     }
     public static void main(String[] args) throws Exception{
          gun(1);
          gun(3.14);
          gun(3.14f);
          gun('m');
          gun("mceiba");
          GenericMethods gm = new GenericMethods();
          gm.fun(1);
          gm.fun(3.14);
          gm.fun(3.14f);
          gm.fun('m');
          gm.fun("mceiba");
     }
}
{% endhighlight %}

为了避免创建容器类时繁琐的泛型类型参数声明，可以使用泛型方法创建一个产生容器的工具包。
{% highlight java linenos %}
// Easy create all kinds of container
package com.mceiba.util;
import java.util.*;
public class Container {
  // create a HashMap:
  public static <K, V> Map<K, V> map(){
    return new HashMap<K, V>();
  }
  // create an ArrayList:
  public static <T> List<T> list(){
    return new ArrayList<T>();
  }
  // create a LinkedList:
  public static <T> List<T> lklist(){
    return new LinkedList<T>();
  }
  // create a Set:
  public static <T> Set<T> set(){
    return new HashSet<T>();
  }
  // create a Queue:
  public static <T> Queue<T> queue(){
    return new LinkedList<T>();
  }
  // create a Stack:
  public static <T> Stack<T> stack(){
    return new Stack<T>();
  }
} 
{% endhighlight %}

泛型方法与可变参数列表也能很好的共存。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;

public class GenericVarargs{
     @SafeVarargs
     public static <T> List<T> makeList(T... args){
          List<T> list = new ArrayList<T>();
          for(T t : args) list.add(t);
          return list;
     }
     public static void main(String[] args){
          List<String> list = makeList("QWERTYUIOP".split(""));
          println(list);
     }
}
{% endhighlight %}

存在这种可能性：参数化类型的变量引用不是该类型的对象，这种情况叫做***堆污染***。这种情况的发生在程序执行了一些在编译期会引起未检查警告的操作。

泛型可以用于生成器，这样就可以构造较为通用的构造器。
{% highlight java linenos %}
interface Generator<T> { T next(); }
class BasicGenerator<T> implements Generator<T>{
     private Class<T> type;
     public BasicGenerator(Class<T> type) { this.type = type; }
     public T next(){
          try{
               return type.newInstance();
          }catch(Exception e){
               throw new RuntimeException(e);
          }
     }
     public static <T> Generator<T> create(Class<T> type){
          return new BasicGenerator<T>(type);
     }
}
{% endhighlight %}

###擦除

在泛型代码内部，无法获得任何有关泛型参数类型的信息。java泛型是通过**擦除**来实现的，这意味着使用泛型时，任何具体的类型信息都被擦除掉了。`List<String>`和`List<Integer>`在运行时实际上是相同的类型，都被擦除为原生类型List。要在泛型内部使用具体的类型信息，需要给定泛型类的边界，以告知编译器只能接受遵循这个边界的类型，这里重用了extends。大多数情况下它不如直接使用基类代替导出类这种较低层次的泛化来的简单，但是如果需要使用跟具体类型相关的功能，而又要返回具体的类型，则只能使用这种给定了边界的泛型。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
public class GenericType<T extends Number>{
     private T t;
     public GenericType(T t) { this.t = t; }
     public T get() { return t; }
     public int getInt() { return t.intValue(); }
     public static void main(String[] args){
          GenericType<Integer> gti = new GenericType<Integer>(1);
          GenericType<Float> gtf = new GenericType<Float>(0.618f);
          GenericType<Double> gtd = new GenericType<Double>(3.14);
          println(gti.get()+", "+gtf.get()+", "+gtd.get());
          println(gti.getInt()+", "+gtf.getInt()+", "+gtd.getInt());
     }
}
{% endhighlight %}

擦除减少了泛型的泛化性，是一种折中的方式，在基于擦除的实现中，泛型类型被当做第二类类型处理，即不能在重要的上下文环境中使用类型，只在静态检查时才出现泛型类型，在此之后所有的泛型类型都将被擦除，替换为它的非泛型上界，容器类被擦除为其原生类，普通类型变量在未指定边界的情况下被擦除为Object，所以不能进行基于类型的语言操作以及反射操作等。

擦除的核心动机是使得泛化的客户端可以使用非泛化的类库，反之亦然，即**迁移兼容性**。虽然移除了有关实际类型的信息，但是编译器可以保证在方法或者类中使用的类型的内部一直性。

泛型中的所有动作都发生在边界处——对传递进来的值进行额外的编译期检查，并插入对传递出去的值的转型，即边界就是发生动作的地方。

偶尔也可以绕过这些问题来编程，比如引入类型标签来对擦除进行补偿，即通过构造器传入一个Class对象，内部还可以使用`isIntance()`，可以使用简单工厂或者模板方法通过泛型来产生需要的对象。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
import java.lang.reflect.*;
interface SimpleFactory<T>{
     T create();
}
abstract class GenericWithCreate<T>{
     final T element;
     GenericWithCreate() { element = create(); }
     abstract T create();
}
class Foo<T>{
     private T x;
     public <F extends SimpleFactory<T>> Foo(F factory){
          x = factory.create();
     }
}
class IntFactory implements SimpleFactory<Integer>{
     public Integer create() { return new Integer(0); }
}
class Widget{
     public static class Factory implements SimpleFactory<Widget>{
          public Widget create(){
               return new Widget();
          }
     }
}
class X {}
class Creator extends GenericWithCreate<X>{
     X create() { return new X(); }
     void fun() { println(element.getClass().getSimpleName()); }
}
class GenericArray<T>{
     private T[] array;
     @SuppressWarnings("unchecked")
     public GenericArray(Class<T> type, int size){
          array = (T[])Array.newInstance(type, size);
     }
     public void put(int index, T item) { array[index] = item; }
     public T get(int  index){ return array[index]; }
     public T[] rep(){ return array; }
}
public class CreatorGeneric{
     public static void main(String[] args){
          new Foo<Integer>(new IntFactory());
          new Foo<Widget>(new Widget.Factory());
          Creator c = new Creator();
          c.fun();
          GenericArray<String> array = new GenericArray<String>(String.class, 5);
          array.put(0, "spam");
          array.put(1, "Hum");
          array.put(2, "Bat");
          array.put(3, "Monty");
          array.put(4, "Opps");
          StringBuilder sd = new StringBuilder("[");
          for(int i=0; i<5; i++){
               sd.append(array.get(i));
               sd.append(", ");
          }
          String str = sd.substring(0, sd.length()-3);
          sd.append("]");
          str+="]";
          println(str);
     }
}
{% endhighlight %}

不能直接使用泛型类型变量来创建数组，一般的解决方案是在需要数组的地方使用ArrayList，也可以将Object数组转型为需要类型，而且需要传入类型标记，以便可以从擦除中恢复类型，一般会产生警告，可以使用`@SuppressWarnings("unchecked")`忽略掉。

设置边界，可以强制规定泛型可以运用的类型，似乎弱化了泛型，但同时一个重要的好处是我们可以按照自己的边界类型来调用方法，这是一个折中的方案。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;

interface HasColor { java.awt.Color getColor(); }
class HoldItem<T>{
     T item;
     HoldItem(T item) { this.item = item; }
     T getItem() { return item; }
}
class Colored<T extends HasColor> extends HoldItem<T>{
     Colored(T item) { super(item); }
     java.awt.Color color() { return item.getColor(); }
}
class Dimension { public int x, y, z; }
//!class ColoredDimension<T extends HasColor & Dimension> //class first, then interface
class ColoredDimension<T extends Dimension & HasColor> extends Colored<T>{
     ColoredDimension(T item) { super(item); }
     int getX() { return item.x; }
     int getY() { return item.y; }
     int getZ() { return item.z; }
}
interface Weight { int weight(); }
class Solid<T extends Dimension & HasColor & Weight> extends ColoredDimension<T>{
     Solid(T item) { super(item); }
     int weight() { return item.weight(); }
}
class Bounded extends Dimension implements HasColor, Weight{
     public java.awt.Color getColor() { return null; }
     public int weight() { return 0; }
}
public class BasicBounds{
     public static void main(String[] args){
          Solid<Bounded> solid = new Solid<Bounded>(new Bounded());
          solid.color();
          solid.getY();
          solid.weight();
     }
}
{% endhighlight %}

###通配符

数组有一种特殊的行为：可以向导出类型的数组赋予基类型的数组引用（只是在编译期允许），这并不适用于容器类。

**通配符**（`?`）经常和extends和super一起工作，来确定上界或者下界，甚至还可以与泛型参数变量一起工作，一般的形式有`<? extends MyClass>`、`<? super MyClass>`、`<? extends T>`，还有一种无解通配符，即`<?>`。在一般情况下，List（持有任何Object类型的原生List）、List<?>（持有某种特定类型的非原生List，只是现在还不知道）和List<Object>（持有任何Object类型的非原生List）是等价的，编译器不会在意他们之间的差别，
{% highlight java linenos %}
Number[] nb = new Integer[5];
     try{
          nb[0] = new Integer(3);
          nb[1] = new Float(3.14f);
          nb[2] = new Double(3.14);
          for(Number num : nb) print(num+" ");
     }catch(Exception e){}

//!List<Number> lt = new ArrayList<Integer>();
List<? extends Number> lts = Arrays.asList(new Integer(0), 3.14f, 3.14);
List<? extends Number> list = new ArrayList<Integer>();
//list.add(3);
{% endhighlight %}

编译器只能验证确切的类型，只关注传递进来和要返回的对象类型，`set()`方法不能工作与Apple和Fruit，因为由于泛型`set()`方法的参数也变成`<? extends Fruit>`，这意味着它是继承与Fruit的任何类型，而编译器无法验证任何类型。
{% highlight java linenos %}
class GenericWritting{
     static <T> void write(List<T> list, T item){
          list.add(item);
     }
     static <T> void writeWithWildcard(List<? super T> list, T item){
          list.add(item);
     }
     static List<Apple> apples = new ArrayList<Apple>();
     static List<Fruit> fruits = new ArrayList<Fruit>();
     public static void testWritting(){
          write(apples, new Apple());
          write(fruits, new Apple());
          writeWithWildcard(apples, new Apple());
          writeWithWildcard(fruits, new Apple());
     }
}
class GenericReading{
     static <T> T read(List<T> list){
          return list.get(0);
     }
     static List<Apple> apples = Arrays.asList(new Apple());
     static List<Fruit> fruits = Arrays.asList(new Fruit());
     static class Reader<T>{
          T read(List<T> list) { return list.get(0); }
     }
     static class CovariantReader<T>{
          T read(List<? extends T> list) { return list.get(0); }
     }
     static void testReading(){
          Apple a =read(apples);
          Fruit f =read(fruits);
          f = read(apples);

          Reader<Fruit> fruitReader = new Reader<Fruit>();
          Fruit ft = fruitReader.read(fruits);
          //!Fruit ft = fruitReader.read(apples);

          CovariantReader<Fruit> coReader = new CovariantReader<Fruit>();
          Fruit cf = coReader.read(fruits);
          Fruit ca = coReader.read(apples);
     }
}
{% endhighlight %}

原生类型、具体参数类型、有界参数类型以及无界通配符参数类型作为参数的接受类型，具有不同的表现形式。
{% highlight java linenos %}
class Hold<T>{
     private T value;
     public Hold() {}
     public Hold(T t) { value = t; }
     public void set(T t) { value = t; }
     public T get() { return value; }
     public boolean equals(Object obj) { return value.equals(obj); }
     public static void testHold(){
          Hold<Apple> apple = new Hold<Apple>(new Apple());
          Apple a = apple.get();
          apple.set(a);
          //!Hold<Fruit> fruit = apple;
          Hold<? extends Fruit> fruit =apple;
          //Fruit f = (Apple)fruit.get();
          //!fruit.set(f);
          try{
               Fruit f = (Orange)fruit.get();
               println(f.getClass().getName());
          }catch(Exception e){ println(e); }
          println(fruit.equals(a));
     }
     static void rawArgs( Hold hold, Object arg){
          //!hold.set(arg);
          Object obj = hold.get();
     }
     static void unboundArgs( Hold<?> hold, Object arg){
          //!!hold.set(arg);
          Object obj = hold.get();
     }
     static <T> T exact1(Hold<T> hold){
          T t = hold.get();
          return t;
     }
     static <T> T exact2(Hold<T> hold, T arg){
          hold.set(arg);
          T t = hold.get();
          return t;
     }
     static <T> T wildSubtype(Hold<? extends T> hold, T arg){
          //!!hold.set(arg);
          T t = hold.get();
          return t;
     }
     static <T> void wildSuptype(Hold<? super T> hold, T arg){
          hold.set(arg);
          //!!T t = hold.get();
          Object obj = hold.get();
     }
     public static void testBound(){
          Hold raw = new Hold();
          Hold<Long> qualified = new Hold<Long>();
          Hold<?> unbound = new Hold<Long>();
          Hold<? extends Long> bounded = new Hold<Long>();
          Long lng = 1L;

          rawArgs(raw , lng);
          rawArgs(qualified , lng);
          rawArgs(unbound , lng);
          rawArgs(bounded , lng);

          unboundArgs(raw , lng);
          unboundArgs(qualified , lng);
          unboundArgs(unbound , lng);
          unboundArgs(bounded , lng);

          //!exact1(raw);
          exact1(qualified);
          exact1(unbound);
          exact1(bounded);

          //!exact2(raw , lng);
          exact2(qualified , lng);
          //!!exact2(unbound , lng);
          //!!exact2(bounded , lng);

          //!wildSubtype(raw , lng);
          wildSubtype(qualified , lng);
          wildSubtype(unbound , lng);
          wildSubtype(bounded , lng);

          //!wildSuptype(raw , lng);
          wildSuptype(qualified , lng);
          //!!wildSuptype(unbound , lng);
          //!!wildSuptype(bounded , lng);

     }
}
{% endhighlight %}

在`rawArgs()`中，编译器知道Hold是一个泛型类型，在这里表示为一个原生类型，所以向`set()`传递任何类型的参数，都被向上转型为Object，无论何时只要使用了原生类型，编译器都会放弃编译期检查，而返回一个警告。

`unbound()`显示`Hold`和`Hold<?>`是不同的，这与`rawArgs()`中的情况是类似的，但给出的是错误信息，因为原生`Hold`可以持有任何类型对象的组合，而`Hold<?>`将持有某种同构类型的集合，不能只向其传递Object。

`exact*()`中要求具体的类型，所以不确定的参数类型传入就会引发错误。

因此，使用确切类型来代替通配符类型的好处是，可以用泛型参数来做更多的事，但是使用通配符使得你必须接受范围更宽的参数化类型作为参数。

**捕捉转换**中需要使用`<?>`而不是原生类型，即如果向一个使用`<?>`的方法传递原生类型，那么编译器可能会推断出实际的类型参数，使用这个方法可以回转并调用另一个使用这个确切类型的方法。
{% highlight java linenos %}
class CaptureConversion{
     static <T> void fun(Hold<T> hold){
          T t = hold.get();
          println(t.getClass().getSimpleName());
     }
     static void hun(Hold<?> hold){
          fun(hold);
     }
     @SuppressWarnings("unchecked")
     static void testCapture(){
          Hold raw = new Hold<Integer>(1);
          fun(raw);  //!
          hun(raw);
          Hold baseRaw = new Hold();
          baseRaw.set(new Object());  //!
          hun(baseRaw);
          Hold<?> wc = new Hold<Double>(3.14);
          hun(wc);
     }
}
{% endhighlight %}

`fun()`中的参数类型时确切的，而在`hun()`中，Hold的参数是一个无界通配符，看起来是未知的，但是在内部被调用的时候发生的是：参数类型在调用`hun()`的过程中被捕获，因此它可以在对`fun()`的调用中被使用。

使用java泛型时会出现的问题：

- 任何基本类型都不能作为类型参数，但是可以使用其包装类或者自动包装机制，需要注意的是自动包装机制并不适用于数组。
- 一个类不能实现同一个泛型接口的两种变体，由于擦除原因，这两个变体会成为相同的接口。
- 使用带有泛型类型参数的转型或instanceof不会有任何效果，如果没有`@SuppressWarnings`注解，编译器会产生警告，这是由于擦除原因，编译器不知道转型是否安全。但是可以使用泛型类来转型，即用新的转型方式（`Class.cast()`）。
- 重载时泛型方法使用不同的类型参数不足以作为不同的方法签名。
- 基类实现了某个泛型接口，导出类就不能再实现该接口的不同变体。

###自限定类型

“古怪的循环（CRG）”是指类相当古怪地出现在自己的基类中这一事实。即类似于这样`class CRG extends GenericType<CRG> {}`，它能够产生使用导出类作为其参数和返回类型的基类，还能将导出类型用作其域类型，甚至将被擦除为Object的类型。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
class BasicHolder<T>{
     T element;
     void set(T arg) { element = arg; }
     T get() { return element; }
     void fun() { println(element.getClass().getSimpleName()); }
}
class Subtype extends BasicHolder<Subtype> {}
public class RecurringGeneric{
     public static void main(String[] args){
          Subtype sb = new Subtype(), st = new Subtype();
          sb.set(st);
          Subtype sp = sb.get();
          sb.fun();
     }
}
{% endhighlight %}

在这里，新类Subtype接受的参数和返回值具有Subtype类型而不仅仅是基类BasicHolder类型。这就是CRG的本质：基类用导出类替代其参数。这意味着泛型基类变成了一种其所有导出类的公共功能的模板，但是这些功能对于其所有参数和返回值，将使用导出类型，也就是说所产生的类中将使用确切类型而不是基类型。

**自限定**采用额外的步骤，强制泛型当做其自己的边界参数来使用。即类似于`class A extends SelfBounded<A> {}`，强制要求将正在定义的类当做参数传递给基类。自限定的意义在于它保证类型参数必须与正在定义的类相同。可以使用不同的自限定类型，但是不能使用不是自限定类型的类型参数。自限定只强作用于继承关系，还可以用作于泛型方法，这可以防止这个方法被用于规定的自限定参数之外的任何事物上。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
class SelfBounded<T extends SelfBounded<T>>{
     T element;
     SelfBounded<T> set(T arg){
          element = arg;
          return this;
     }
     T get() { return element; }
     void fun() { println(element.getClass().getSimpleName()); }
}
class A extends SelfBounded<A> {}
class B extends SelfBounded<A> {}
class C extends SelfBounded<C>{
     C setAndGet(C c) { set(c); return get(); }
}
class SelfBoundingMethod{
     static <T extends SelfBounded<T>> T fun(T arg){
          return arg.set(arg).get();
     }
}
interface GenericGetter<T extends GenericGetter<T>> { T get(); }
interface Getter extends GenericGetter<Getter> {}
public class SelfBound{
     static void test(Getter g){
          Getter gt = g.get();
          GenericGetter gg = g.get();
     }
     public static void main(String[] args){
          A a = new A();
          a.set(new A());
          a = a.set(new A()).get();
          a = a.get();
          C c = new C();
          c = c.setAndGet(new C());
          A s = SelfBoundingMethod.fun(new A());
     }
}
{% endhighlight %}

自限定类型的价值在于它们可以产生**协变**参数类型，即方法参数类型会随子类而变化。协变参数类型允许返回更具体的类型，即子类中与父类方法签名一致，但是返回类型是父类返回类型的某个子类时就可以覆盖。在使用自限定类型时，在导出类中只有一个方法，并且这个方法接受导出类型而不是基类型为参数。

由于擦除的原因，将泛型应用于异常是非常受限的。catch语句不能捕获泛型类型的异常，因为在编译期和运行期都必须知道异常的确切类型。泛型类也不能直接或间接继承自Thowable，但是类型参数可能会在一个方法的throws子句中用到，这样throw语句抛出的异常可以被捕获到。

###混型

**混型**最基本的含义是混合多个类的能力，以产生一个可以表示混型中所有类型的类。混型的价值之一是它可以将特性和行为一致地应用于多个类之上，如果想在混型类中修改某些东西，这些修改将会应用于混型所应用的所有类型之上。

java常见的解决方案是使用接口来产生混型效果，基本上是使用代理，每个混入对象都要求在混型类中有一个相应的域，并编写所需的方法，将调用转发给适当的对象。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
interface TimeStamp { long getStamp(); }
class TimeStampImpl implements TimeStamp{
     private final long timeStamp;
     public TimeStampImpl() { timeStamp = new Date().getTime(); }
     public long getStamp() { return timeStamp; }
}
interface SerialNumber { long getSerialNumber(); }
class SerialNumberImpl implements SerialNumber{
     private static long counter = 1;
     private final long serialNumber = counter++;
     public long getSerialNumber() { return serialNumber; }
}
interface Basic{
     void set(String val);
     String get();
}
class BasicImpl implements Basic{
     private String value;
     public void set(String val) { value = val; }
     public String get() { return value; }
}
class Mixin extends BasicImpl implements TimeStamp, SerialNumber{
     private TimeStamp timeStamp = new TimeStampImpl();
     private SerialNumber serialNumber = new SerialNumberImpl();
     public long getStamp() { return timeStamp.getStamp(); }
     public long getSerialNumber() { return serialNumber.getSerialNumber(); }
}
public class Mixins{
     public static void main(String[] args){
          Mixin mixin1 = new Mixin(), mixin2 = new Mixin();
          mixin1.set("test string 1");
          mixin2.set("test string 2");
          println(mixin1.get()+" "+mixin1.getStamp()+" "+mixin1.getSerialNumber());
          println(mixin2.get()+" "+mixin2.getStamp()+" "+mixin2.getSerialNumber());
     }
}
{% endhighlight %}

**装饰器模式**使用分层对象来动态透明的地向单个对象中添加责任。装饰器指定包装在最初的对象周围的所有对象都具有相同的基本接口。某些事物是可装饰的，可以通过将其他类包装在这个可装饰对象的四周，来将功能分层。装饰器是通过使用组合和形式化结构（可装饰物/装饰器层次结构）来实现的，而混型是基于继承的。基于参数化类型的混型可以当做是一种泛型装饰器机制，这种机制不需要装饰器模式的继承结构。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
class Basic{
     private String value;
     public void set(String val) { value = val; }
     public String get() { return value; }
}
class Decorator extends Basic{
     protected Basic basic;
     public Decorator(Basic basic) { this.basic = basic; }
     public void set(String val) { basic.set(val); }
     public String get() { return basic.get(); }
}
class TimeStamp extends Decorator{
     private final long timeStamp;
     public TimeStamp(Basic basic){
          super(basic);
          timeStamp = new Date().getTime();
     }
     public long getStamp() { return timeStamp; }
}
class SerialNumber extends Decorator{
     private static long counter = 1;
     private final long serialNumber = counter++;
     public SerialNumber(Basic basic) { super(basic); }
     public long getSerialNumber() { return serialNumber; }
}
public class Decoration{
     public static void main(String[] args){
          TimeStamp ts = new TimeStamp(new Basic());
          SerialNumber sn = new SerialNumber(new Basic());
     }
}
{% endhighlight %}

装饰器只是对混型提出的问题的一种局限的解决方案，因为它只能有效地工作于装饰中的最后一层，使用动态代理可以创建一种比装饰器更贴近混型模型的机制。

我们总是在设法编写能够尽可能广泛的应用的代码，为实现这一点，我们需要放松对代码将要作用的类型的限制，同时又不丢失静态类型检查的好处。泛型像这方面迈进了一步，至少在持有对象时，但是要想在泛型类型上执行操作就会产生问题，由于擦除的原因使得泛化受到限制。一些编程语言提供的解决方案称为**潜在类型机制**或者结构化类型机制，又或者**鸭子类型机制**，即“如果它走起来像鸭子，并且叫起来也像鸭子，那么你就可以把它当鸭子对待”。

###潜在类型机制

潜在类型机制是一种代码组织和复用机制，使用它的代码可以更容易的复用，C++、Python、Ruby等都支持潜在类型机制。
{% highlight python linenos %}
class Duck:
     def move(self):
          print("Duck moving")
     def speak(self):
          print("Duck speaking ...")
     def fun(self):
          pass
class Robot:
     def move(self):
          print("Robot moving")
     def speak(self):
          print("Robot speaking ...")
     def fun(self):
          pass
def perform(anything):
     anything.move()
     anything.speak()

if __name__ == '__main__':
     duck = Duck()
     robot = Robot()
     perform(duck)  
     perform(robot) 
#Duck moving
#Duck speaking ...
#Robot moving
#Robot speaking ... 
{% endhighlight %}

而在java中由于泛型机制是后期加入的，所以并未提供潜在类型机制的支持，类似的效果必须强制使用实现自相同的接口或有共同的父类。但是这并不意味着有界泛型代码不能在不同的类型层次结构之间应用，我们可以使用其他方式创建真正的泛型代码。

可以使用的一种方式是反射。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.lang.reflect.*;
class Mime{
     public void walk() {}
     public void sit() { println("Pretending to sit"); }
     public void push() {}
     public String toString() { return "Mime"; }
}
class Dog{
     public void speak() { println("Woof!"); }
     public void sit() { println("Sitting"); }
     public void reproduce() {}
}
class Robot{
     public void speak() { println("Click!"); }
     public void sit() { println("Clank"); }
     public void oilChange() {}
}
class Communicate{
     public static void perform(Object speaker){
          Class<?> spk = speaker.getClass();
          try{
               try{
                    Method speak = spk.getMethod("speak");
                    speak.invoke(speaker);
               }catch(NoSuchMethodException e){
                    println(speaker+" cannot speak");
               }
               try{
                    Method sit = spk.getMethod("sit");
                    sit.invoke(speaker);
               }catch(NoSuchMethodException e){
                    println(speaker+" cannot sit");
               }
          }catch(Exception e){
               throw new RuntimeException(speaker.toString(), e);
          }
     }
}
public class LatentReflection{
     public static void main(String[] args){
          Communicate.perform(new Dog());
          Communicate.perform(new Robot());
          Communicate.perform(new Mime());
     }
}
{% endhighlight %}

反射实现了一些有趣的可能性，但是它将所有类型检查都转移到了运行时，如果能够实现编译期的类型检查，这通常更符合要求。

这里有一个将一个方法应用于序列的例子。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.lang.reflect.*;
import java.util.*;
class Apply{
     public static <T, S extends Iterable<? extends T>>
     void apply(S seq, Method f, Object... args){
          try{
               for(T t: seq) f.invoke(t, args);
          }catch(Exception e){
               throw new RuntimeException(e);
          }
     }
}
class Shape{
     public void rotate() { println(this+" rotate"); }
     public void resize(int size) { println(this+" resize "+size); }
}
class Square extends Shape {}
class FilledList<T> extends ArrayList<T>{
     public FilledList(Class<? extends T> type, int size){
          try{
               for(int i=0; i<size; i++)
                    add(type.newInstance());
          }catch(Exception e){
               throw new RuntimeException(e);
          }
     }
}
class SimpleQueue<T> implements Iterable<T>{
     private LinkedList<T> storage = new LinkedList<T>();
     public void add(T t) { storage.offer(t); }
     public T get() { return storage.poll(); }
     public Iterator<T> iterator() { return storage.iterator(); }
}
public class TypeMark{
     public static void main(String[] args) throws Exception{
          List<Shape> shapes = new ArrayList<Shape>();
          for(int i=0; i<10; i++)
               shapes.add(new Shape());
          Apply.apply(shapes, Shape.class.getMethod("rotate"));
          Apply.apply(shapes, Shape.class.getMethod("resize", int.class), 5);
          List<Square> squares = new ArrayList<Square>();
          for(int i=0; i<10; i++)
               squares.add(new Square());
          Apply.apply(squares, Shape.class.getMethod("rotate"));
          Apply.apply(squares, Shape.class.getMethod("resize", int.class), 5);

          Apply.apply(new FilledList<Shape>(Shape.class, 10),
               Shape.class.getMethod("rotate"));
          Apply.apply(new FilledList<Shape>(Shape.class, 10),
               Shape.class.getMethod("resize", int.class), 5);

          SimpleQueue<Shape> shapeQ = new SimpleQueue<Shape>();
          for(int i=0; i<5; i++){
               shapeQ.add(new Shape());
               shapeQ.add(new Square());
          }
          Apply.apply(shapeQ, Shape.class.getMethod("rotate"));
     }
}
{% endhighlight %}

上面的例子确实很优雅，但是由于只能作用于实现了Iterable接口的对象，因此还是受限的，当我们没有合适的接口时，自然想到了适配器模式，用我们使用适配器来适配已有的接口，以产生想要的接口。使用适配器能编写出真正的泛型代码，作为java中比较好的潜在类型机制替代方案。
{% highlight java linenos %}
import java.util.*;
import static com.mceiba.util.Print.*;
interface Addable<T> { void add(T t); }
class Fill {
  // Classtoken version:
  public static <T> void fill(Addable<T> addable,
  Class<? extends T> classToken, int size) {
    for(int i = 0; i < size; i++)
      try {
        addable.add(classToken.newInstance());
      } catch(Exception e) {
        throw new RuntimeException(e);
      }
  }
  // Generator version:
  public static <T> void fill(Addable<T> addable,
  Generator<T> generator, int size) {
    for(int i = 0; i < size; i++)
      addable.add(generator.next());
  }
}

// To adapt a base type, you must use composition.
// Make any Collection Addable using composition:
class AddableCollectionAdapter<T> implements Addable<T> {
  private Collection<T> c;
  public AddableCollectionAdapter(Collection<T> c) {
    this.c = c;
  }
  public void add(T item) { c.add(item); }
}
    
// A Helper to capture the type automatically:
class Adapter {
  public static <T> Addable<T> collectionAdapter(Collection<T> c) {
    return new AddableCollectionAdapter<T>(c);
  }
}

// To adapt a specific type, you can use inheritance.
// Make a SimpleQueue Addable using inheritance:
class AddableSimpleQueue<T>
extends SimpleQueue<T> implements Addable<T> {
  public void add(T item) { super.add(item); }
}
    
public class FillTest {
  public static void main(String[] args) {
    // Adapt a Collection:
    List<Coffee> carrier = new ArrayList<Coffee>();
    Fill.fill(
      new AddableCollectionAdapter<Coffee>(carrier),
      Coffee.class, 3);
    // Helper method captures the type:
    Fill.fill(Adapter.collectionAdapter(carrier),
      Latte.class, 2);
    for(Coffee c: carrier)
      println(c);
    println("----------------------");
    // Use an adapted class:
    AddableSimpleQueue<Coffee> coffeeQueue =
      new AddableSimpleQueue<Coffee>();
    Fill.fill(coffeeQueue, Mocha.class, 4);
    Fill.fill(coffeeQueue, Latte.class, 1);
    for(Coffee c: coffeeQueue)
      println(c);
  }
} /* Output:
Coffee 0
Coffee 1
Coffee 2
Latte 3
Latte 4
----------------------
Mocha 5
Mocha 6
Mocha 7
Mocha 8
Latte 9
*///:~
{% endhighlight %}

##第16章 数组

###数组的基本操作

数组与其他容器的区别有三方面：效率、类型和保存基本类型的能力。在java中数组是一种效率最高的存储和随机访问对象引用序列的方式。数组是一个简单的线性序列，元素访问非常快，代价是大小被固定，在其生命周期中不可变。数组和容器也都不能越界，否则会产生异常。泛型之前的容器不能持有基本类型，只有数组可以。数组与现在的容器相比仅存的优势效率。

数组的`[]`语法是访问数组对象唯一的方式，只读成员`length`（返回数组的大小而不是元素的个数）是数组对象的一部分，也是数组唯一可以访问的字段或方法。对象数组和基本类型数组唯一的区别是对象数组保存的是引用，基本类型数组直接保存的是基本类型的值。

数组标示符其实只是一个引用，指向在堆中创建的一个真实的数组对象，这个对象用以保存指向其他对象的引用。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
class Sphere{
     private static long counter;
     private final long id = counter++;
     public String toString() { return " Sphere "+id; }
}
class ArrayOptions{
     public static void main(String[] args){
          Sphere[] a;
          Sphere[] b = new Sphere[5];
          println("b: "+Arrays.toString(b));
          Sphere[] c = new Sphere[4];
          for(int i=0; i<c.length; i++)
               if(c[i]==null)
                    c[i]=new Sphere();
          Sphere[] d = {new Sphere(), new Sphere(), new Sphere()};
          a = new Sphere[]{new Sphere(), new Sphere()};
          println("a.length = "+a.length);
          println("b.length = "+b.length);
          println("c.length = "+c.length);
          println("d.length = "+d.length);
          a = d;
          println("a.length = "+a.length);

          int[] e;
          int[] f = new int[5];
          println("f: "+Arrays.toString(f));
          int[] g = new int[4];
          for(int i=0; i<g.length; i++)
               g[i]=i*i;
          int[] h = {11, 22, 33};
          //1println("e.length = "+e.length);
          println("f.length = "+f.length);
          println("g.length = "+g.length);
          println("h.length = "+h.length);
          e = h;
          println("e.length = "+e.length);
          e = new int[]{1, 2};
          println("e.length = "+e.length);
     }
}
{% endhighlight %}

**粗糙数组**中构成矩阵的每个向量都可以具有任意的长度，使用`Arrays.deepToString()`可以深层次的显示数组，对基本类型和对象都适用。C++中只能返回数组的引用，而java可以返回数组本身，就像其他的对象一样。
{% highlight java linenos %}
static char[] getChar(int num){
     num = (num>26)? 26:num;
     char[] chr = new char[num];
     Random rand = new Random();
     for(int i=0; i<num; i++)
          chr[i] = ch[rand.nextInt(num-1)];
     return chr;
}
char[] ch = new char[3];
boolean[] bl = new boolean[3];
println("ch: "+Arrays.toString(ch));
println("bl: "+Arrays.toString(bl));
char[] chr = getChar(10);
println("chr: "+Arrays.toString(chr));
int[][][] k = new int[2][3][4];
int[][] m = {
     {1, 2, 3},
     {4, 5, 6},
     {7, 8, 9},
} ;
println("m: "+Arrays.toString(m));
println("m: "+Arrays.deepToString(m));
Random rand = new Random();
int[][][] kk = new int[5][][];
for(int i=0; i<kk.length; i++){
     kk[i]=new int[rand.nextInt(5)][];
     for(int j=0; j<kk[i].length; j++)
          kk[i][j] = new int[rand.nextInt(5)];
}
println("kk: "+Arrays.deepToString(kk));
{% endhighlight %}

###数组与泛型

通常数组与泛型不能很好的结合，不能实例化具有参数类型的数组，擦除会移除参数类型信息，而数组必须知道他们所持有的确切类型，但是可以参数化数组本身的类型。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
class ClassParam<T>{
     public T[] fun(T[] arg) { return arg; }
}
class MethodParam{
     public static <T> T[] fun(T[] arg) { return arg; }
}
class ArrayType{
     @SuppressWarnings("unchecked")
     public static void main(String[] args){
          Integer[] in = {1, 2, 3, 4, 5};
          Double[] dou = {1.1, 2.2, 3.3, 4.4, 5.5};

          Integer[] inte = new ClassParam<Integer>().fun(in);
          Double[] doub = MethodParam.fun(dou);

          List<String>[] ls;
          List[] la = new List[5];
          ls = (List<String>[])la;
          ls[0] = new ArrayList<String>();
          //!ls[1] = new ArrayList<Integer>();
          Object[] obj = ls;
          obj[1] = new ArrayList<Integer>();
          List<Sphere>[] sphere = (List<Sphere>[])new List[5];
          for(int i=0; i<sphere.length; i++){
               sphere[i] = new ArrayList<Sphere>();
               sphere[i].add(new Sphere());
               sphere[i].add(new Sphere());
          }
          println("sphere: "+Arrays.toString(sphere));
     }
}
{% endhighlight %}

不能直接创建泛型数组，但是可以创建非泛型的数组，然后将其转型。由于数组是协变类型的，可以将不同参数类型的容器赋值给数组，因为他们都可以看做是Object。

###Arrays的实用功能

- `Arrays.fill()`：可以用单一的数值来填充整个数组或者数组的某个区域。
- `System.arraycopy()`：复制数组比for循环要快，但是只是浅复制，对于对象只是复制了引用。
- `Arrays.equals()`：重载后可以比较整个数组，数组相等的条件是元素个数相等，且对应位置元素也相等。也可以对每个元素使用`equals()`（基本类型使用包装类的`equals()`方法）。
- `Arrays.sort()`：实现comparable接口（只有一个`compareTo()`方法）或者具有相关联的Comparator，就可以进行比较和排序。
- `Arrays.binarySearch()`：对已排序的的数组进行快速查找
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
class CompType implements Comparable<CompType>{
     int i, j;
     private static int count = 1;
     public CompType(int i, int j) { this.i = i; this.j = j; }
     public String toString(){
          String result = "[ i = "+i+", j = "+j+" ]";
          if(count++%3 == 0) result+="\n";
          return result;
     }
     public int compareTo(CompType rv){
          return (i<rv.i ? -1 : (i==rv.i ? 0 : 1));
     }
}
class ArraysFunc{
     public static void main(String[] args){
          int[] in = {1, 2, 3, 4, 5};
          Arrays.fill(in, 33);
          println("fill in: "+Arrays.toString(in));
          Arrays.fill(in, 1, 4, 99);
          println("fill in: "+Arrays.toString(in));

          int[] out = new int[10];
          System.arraycopy(in, 0, out, 0, in.length);
          println("copy out: "+Arrays.toString(out));
          println(Arrays.equals(in, out));

          Random rand = new Random();
          CompType[] com  =new CompType[9];
          for(int i=0; i<com.length; i++)
               com[i]=new CompType(rand.nextInt(100), rand.nextInt(100));
          println("before sort:");
          println(Arrays.toString(com));
          Arrays.sort(com);
          println("after sort:");
          println(Arrays.toString(com));

          int[] bs = new int[10];
          for(int i=0; i<bs.length; i++)
               bs[i] = rand.nextInt(50);
          Arrays.sort(bs);
          int sb = Arrays.binarySearch(bs, bs[8]);
          println("binarySearch bs: "+Arrays.toString(bs)+", "+bs[8]+": "+sb);
     }
}
{% endhighlight %}

##第17章 容器深入研究

##第18章 Java I/O系统

##第19章 枚举类型

###基本enum特性

`values()`可以返回enum实例的数组，元素顺序严格保持在enum中声明的顺序。创建enum时编译器会生成一个相关的类（final型），这个类继承自java.lang.Enum。enum除了不能继承外，基本上可以看做一个类，即可以添加方法（实例在最开始），甚至可以有main方法。enum可以有自己的构造器，但是只能在内部使用创建enum实例，即在enum定义结束后，编译器不允许再使用构造器。enum还可以覆盖方法，比如`toString()`。

在switch语句的case中使用enum实例可以不用enum类型来修饰。`values()`方法是由编译器插入到enum中的static方法，如果将enum向上转型为Enum，`values()`方法将不可用，不过可以用`Class.getEnumConstants()`方法获得所有的enum实例。

enum都继承自Enum，所以就不能继承其他的类，但是可以实现接口。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import com.mceiba.util.OSExecute;
import java.util.*;
import java.lang.Enum;
enum Shrubbery { GROUND, CRAWLING, HANGING }
enum OzWitch{
     WEST("Miss Gulch, aka the Wiched Witch of the West"),
     NORTH("Glinda, the Good Witch of the North"),
     EAST("Wicked Witch of the East, wearer of the Ruby"),
     SOUTH("Good by inference, but missing");
     private String description;
     private OzWitch(String description) { this.description = description; }
     public String getDescription() { return description; }
     public String toString(){
          String id = name();
          String lower = id.substring(1).toLowerCase();
          return id.charAt(0)+lower;
     }
     public static void test(){
          for(OzWitch ow : OzWitch.values()){
               println(ow+": "+ow.getDescription());
          }
     }
}
enum Signal { GREEN, YELLOW, RED }
public class EnumClass{
     Signal color = Signal.RED;
     public void change(){
          switch(color){
               case RED : color = Signal.YELLOW; break;
               case YELLOW : color = Signal.GREEN; break;
               case GREEN : color = Signal.RED; break;
          }
     }
     public String toString(){
          return "This tranffic light is "+color;
     }
     public static void main(String[] args){
          for(Shrubbery sb : Shrubbery.values()){
               println(sb+" ordinal: "+sb.ordinal()+", DeclaringCalss: "+
                    sb.getDeclaringClass()+", name: "+sb.name());
          }
          Enum eu = Signal.GREEN;
          //!eu.values();
          for(Enum en : eu.getClass().getEnumConstants())
               print(en+" ");
          println();
          for(String st : "GROUND CRAWLING HANGING".split(" ")){
               Shrubbery sby = Enum.valueOf(Shrubbery.class, st);
               println(sby);
          }
          OzWitch.test();
          EnumClass tranffic = new EnumClass();
          for(int i=0; i<5; i++){
               println(tranffic);
               tranffic.change();
          }
          OSExecute.command("javap Signal");
     }
}
{% endhighlight %}

###组织枚举
 
在一个接口的内部，创建实现该接口的枚举，可以达到将枚举元素分类组织的目的。还可以使用嵌套的方式来组织枚举。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import java.util.*;
import java.lang.Enum;
interface Food{
     enum Appetizer implements Food{
          SALAD, SOUP, SPARING_ROLLS;
     }
     enum MainCourse implements Food{
          LASAGNE, BURRITO, PAD_THAI,
          LENTILS, HUMMOUS, VINDALOO;
     }
     enum Dessert implements Food{
          TIRAMISU, GELATO, BLACK_FORSET,
          FRUIT, CREME_CARAMEL;
     }
     enum Coffee implements Food{
          BLACK_COFFEE, DECAF_COFFEE, ESPRESSO,
          LATTE, CAPPUCCINO, TEA, HERB_TEA;
     }
}
enum Course{
     APPETIZER(Food.Appetizer.class),
     MAINCOURSE(Food.MainCourse.class),
     DESSERT(Food.Dessert.class),
     COFFEE(Food.Coffee.class);
     private Food[] values;
     private Course(Class<? extends Food> kind){
          values = kind.getEnumConstants();
     }
     Random rand = new Random();
     public Food getValue() { return values[rand.nextInt(values.length)]; }
}
enum Meal{
     APPETIZER(Food.Appetizer.class),
     MAINCOURSE(Food.MainCourse.class),
     DESSERT(Food.Dessert.class),
     COFFEE(Food.Coffee.class);
     private Food[] values;
     public interface Food{
          enum Appetizer implements Food{
               SALAD, SOUP, SPARING_ROLLS;
          }
          enum MainCourse implements Food{
               LASAGNE, BURRITO, PAD_THAI,
               LENTILS, HUMMOUS, VINDALOO;
          }
          enum Dessert implements Food{
               TIRAMISU, GELATO, BLACK_FORSET,
               FRUIT, CREME_CARAMEL;
          }
          enum Coffee implements Food{
               BLACK_COFFEE, DECAF_COFFEE, ESPRESSO,
               LATTE, CAPPUCCINO, TEA, HERB_TEA;
          }
     }
     private Meal(Class<? extends Food> kind){
          values = kind.getEnumConstants();
     }
     Random rand = new Random();
     public Food getValue() { return values[rand.nextInt(values.length)]; }
}
public class TypeOfFood{
     public static void main(String[] args){
          for(int i=0; i<2; i++){
               for(Course course : Course.values()){
                    Food food = course.getValue();
                    println(food);
               }
               println("-----");
          }
          for(int i=0; i<2; i++){
               for(Meal meal : Meal.values()){
                    Meal.Food food = meal.getValue();
                    println(food);
               }
               println("-----");
          }
     }
}
{% endhighlight %}

**EnumSet**可以替代传统的基于int的位标识，这种标志同样表示“开/关”的信息，取代bit，它的内部是将一个long值作为比特向量，同时保证了速度而又更容易让人理解。
{% highlight java linenos %}
EnumSet<Signal> num = EnumSet.noneOf(Signal.class);
num.add(Signal.RED);
println(num);
num.addAll(EnumSet.of(Signal.YELLOW, Signal.GREEN));
println(num);
num.removeAll(EnumSet.of(Signal.YELLOW, Signal.GREEN));
println(num);
num = EnumSet.allOf(Signal.class);
println(num);
{% endhighlight %}

**EnumMap**是一种特殊的Map，其中的key必须来自于一个enum，而且enum作为一个实例的键总是存在的，如果没有调用`put()`方法存入值，对应的值就是null。
{% highlight java linenos %}
interface Command { void action(); }
enum Signal { RED, YELLOW, GREEN }
public class Commands{
     public static void main(String[] args){
          EnumMap<Signal, Command> em = new EnumMap<Signal, Command>(Signal.class);
          em.put(Signal.RED, new Command(){
               public void action() { println("stop"); }
          });
          em.put(Signal.YELLOW, new Command(){
               public void action() { println("wait"); }
          });
          em.put(Signal.GREEN, new Command(){
               public void action() { println("go"); }
          });
          for(Map.Entry<Signal, Command> e : em.entrySet()){
               print(e.getKey()+": ");
               e.getValue().action();
          }
     }
}
{% endhighlight %}

这里使用了**命令模式**，即首先需要一个只有单一方法的接口，然后从该接口实现具有不同行为的多了类，接下来构建命令对象，并且根据对象实现不同的行为。

###常量相关的方法

enum允许为enum实例编写方法，从而可以为每个enum实例赋予不同的行为。要实现**常量相关的方法**，需要为enum定义一个或多个abstract方法，然后为每个enum实例实现该抽象方法，也可以定义一般的方法，并且在enum实例中可以覆盖。这通常称为**表驱动的代码**（table-driven code），与命令模式具有某种相似之处。

enum的实例与类也有一些相似之处，在调用常量相关的方法时表现出了“多态”的行为，但是相似之处也仅限于此，并不能将enum实例作为一个类来使用。每个enum元素都是enum类型放入static final实例。
{% highlight java linenos %}
public enum ConstantMethod{
     DATETIME{
          void fun() { println("Current date"); }
          String getInfo(){
               return DateFormat.getDateInstance().format(new Date());
          }
     },
     CLASSPATH{
          String getInfo(){
               return System.getenv("CLASSPATH");
          }
     },
     VERSION{
          void fun() { println("System JDK version"); }
          String getInfo(){
               return System.getProperty("java.version");
          }
     };
     abstract String getInfo();
     void fun() { println("default method"); }
     public static void main(String[] args){
          for(ConstantMethod cm : values()){
               cm.fun();
               println(cm.getInfo());
          }
     }
}
{% endhighlight %}

通过常量相关的方法还可以实现简单的**责任链模式**，即以多种不同的方法来解决一个问题，然后将这些方法链接在一起，当一个请求到来时，遍历整个链，直到链中的某个解决方案（每一个解决方案可以当做一种策略）可以处理该请求。
{% highlight java linenos %}
import java.util.*;
import static com.mceiba.util.Print.*;
class Enums{
  public static <T extends Enum<T>> T random(Class<T> type){
    Random rand = new Random();
    return type.getEnumConstants()[rand.nextInt(type.getEnumConstants().length)];
  }
}
class Mail {
  // The NO's lower the probability of random selection:
  enum GeneralDelivery {YES,NO1,NO2,NO3,NO4,NO5}
  enum Scannability {UNSCANNABLE,YES1,YES2,YES3,YES4}
  enum Readability {ILLEGIBLE,YES1,YES2,YES3,YES4}
  enum Address {INCORRECT,OK1,OK2,OK3,OK4,OK5,OK6}
  enum ReturnAddress {MISSING,OK1,OK2,OK3,OK4,OK5}
  GeneralDelivery generalDelivery;
  Scannability scannability;
  Readability readability;
  Address address;
  ReturnAddress returnAddress;
  static long counter = 0;
  long id = counter++;
  public String toString() { return "Mail " + id; }
  public String details() {
    return toString() +
      ", General Delivery: " + generalDelivery +
      ", Address Scanability: " + scannability +
      ", Address Readability: " + readability +
      ", Address Address: " + address +
      ", Return address: " + returnAddress;
  }
  // Generate test Mail:
  public static Mail randomMail() {
    Mail m = new Mail();
    m.generalDelivery= Enums.random(GeneralDelivery.class);
    m.scannability = Enums.random(Scannability.class);
    m.readability = Enums.random(Readability.class);
    m.address = Enums.random(Address.class);
    m.returnAddress = Enums.random(ReturnAddress.class);
    return m;
  }
  public static Iterable<Mail> generator(final int count) {
    return new Iterable<Mail>() {
      int n = count;
      public java.util.Iterator<Mail> iterator() {
        return new java.util.Iterator<Mail>() {
          public boolean hasNext() { return n-- > 0; }
          public Mail next() { return randomMail(); }
          public void remove() { // Not implemented
            throw new UnsupportedOperationException();
          }
        };
      }
    };
  }
}

public class Chain {
  enum MailHandler {
    GENERAL_DELIVERY {
      boolean handle(Mail m) {
        switch(m.generalDelivery) {
          case YES:
            println("Using general delivery for " + m);
            return true;
          default: return false;
        }
      }
    },
    MACHINE_SCAN {
      boolean handle(Mail m) {
        switch(m.scannability) {
          case UNSCANNABLE: return false;
          default:
            switch(m.address) {
              case INCORRECT: return false;
              default:
                println("Delivering "+ m + " automatically");
                return true;
            }
        }
      }
    },
    VISUAL_INSPECTION {
      boolean handle(Mail m) {
        switch(m.readability) {
          case ILLEGIBLE: return false;
          default:
            switch(m.address) {
              case INCORRECT: return false;
              default:
                println("Delivering " + m + " normally");
                return true;
            }
        }
      }
    },
    RETURN_TO_SENDER {
      boolean handle(Mail m) {
        switch(m.returnAddress) {
          case MISSING: return false;
          default:
            println("Returning " + m + " to sender");
            return true;
        }
      }
    };
    abstract boolean handle(Mail m);
  }
  static void handle(Mail m) {
    for(MailHandler handler : MailHandler.values())
      if(handler.handle(m))
        return;
    println(m + " is a dead letter");
  }
  public static void main(String[] args) {
    for(Mail mail : Mail.generator(10)) {
      println(mail.details());
      handle(mail);
      println("*****");
    }
  }
} /* Output:
Mail 0, General Delivery: NO2, Address Scanability: UNSCANNABLE, Address Readability: YES3, Address Address: OK1, Return address: OK1
Delivering Mail 0 normally
*****
Mail 1, General Delivery: NO5, Address Scanability: YES3, Address Readability: ILLEGIBLE, Address Address: OK5, Return address: OK1
Delivering Mail 1 automatically
*****
Mail 2, General Delivery: YES, Address Scanability: YES3, Address Readability: YES1, Address Address: OK1, Return address: OK5
Using general delivery for Mail 2
*****
Mail 3, General Delivery: NO4, Address Scanability: YES3, Address Readability: YES1, Address Address: INCORRECT, Return address: OK4
Returning Mail 3 to sender
*****
Mail 4, General Delivery: NO4, Address Scanability: UNSCANNABLE, Address Readability: YES1, Address Address: INCORRECT, Return address: OK2
Returning Mail 4 to sender
*****
Mail 5, General Delivery: NO3, Address Scanability: YES1, Address Readability: ILLEGIBLE, Address Address: OK4, Return address: OK2
Delivering Mail 5 automatically
*****
Mail 6, General Delivery: YES, Address Scanability: YES4, Address Readability: ILLEGIBLE, Address Address: OK4, Return address: OK4
Using general delivery for Mail 6
*****
Mail 7, General Delivery: YES, Address Scanability: YES3, Address Readability: YES4, Address Address: OK2, Return address: MISSING
Using general delivery for Mail 7
*****
Mail 8, General Delivery: NO3, Address Scanability: YES1, Address Readability: YES3, Address Address: INCORRECT, Return address: MISSING
Mail 8 is a dead letter
*****
Mail 9, General Delivery: NO1, Address Scanability: UNSCANNABLE, Address Readability: YES2, Address Address: OK1, Return address: OK4
Delivering Mail 9 normally
*****
*///:~
{% endhighlight %}

枚举类型非常适合用来创建**状态机**，状态机一般具有多个特定的状态，根据输入从一个状态转移到下了一个状态，但是也可能存在瞬时状态。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import com.mceiba.util.TextFile;
import java.util.*;

interface Generator<T> { T next(); }
enum Input{
     NICKEL(5), DIME(10), QUARTER(25), DOLLAR(100),
     TOOTHPASTE(200), CHIPS(75), SODA(100), SOAP(50),
     ABORT_TRANSACTION{
          public int amount(){
               throw new RuntimeException("ABORT.amount()");
          }
     },
     STOP{
          public int amount(){
               throw new RuntimeException("SHUT_DOWN.amount()");
          }
     };
     int value;
     Input(int value) { this.value = value; }
     Input() {}
     int amount() { return value; }
     static Random rand = new Random();
     public static Input randomSelection(){
          return values()[rand.nextInt(values().length-1)];
     }
}
enum Category{
     MONEY(Input.NICKEL, Input.DIME, Input.QUARTER, Input.DOLLAR),
     ITEM_SELECTION(Input.TOOTHPASTE, Input.CHIPS, Input.SODA, Input.SOAP),
     QUIT_TANSACTION(Input.ABORT_TRANSACTION),
     SHUT_DOWN(Input.STOP);
     private Input[] values;
     Category(Input... types) { values = types; }
     private static EnumMap<Input, Category> categories =
     new EnumMap<Input, Category>(Input.class);
     static{
          for(Category c : Category.class.getEnumConstants())
               for(Input type : c.values)
                    categories.put(type, c);
     }
     public static Category categorize(Input input){
          return categories.get(input);
     }
}
public class VendingMachine{
     private static State state = State.RESTING;
     private static int amount = 0;
     private static Input selection = null;
     enum StateDuration { TRANSIENT }
     enum State{
          RESTING{
               void next(Input input){
                    switch(Category.categorize(input)){
                         case MONEY:
                              amount += input.amount();
                              state = ADDING_MONEY;
                              break;
                         case SHUT_DOWN:
                              state = TERMINAL;
                         default:
                    }
               }
          },
          ADDING_MONEY{
               void next(Input input){
                    switch(Category.categorize(input)){
                         case MONEY:
                              amount += input.amount();
                              break;
                         case ITEM_SELECTION:
                              selection = input;
                              if(amount<selection.amount())
                                   println("Insufficient money for "+selection);
                              else state = DISPENSING;
                              break;
                         case QUIT_TANSACTION:
                              state = GIVING_CHANGE;
                              break;
                         case SHUT_DOWN:
                              state = TERMINAL;
                         default:
                    }
               }
          },
          DISPENSING(StateDuration.TRANSIENT){
               void next(){
                    println("Here is your  "+amount);
                    amount -= selection.amount();
                    state = GIVING_CHANGE;
               }
          },
          GIVING_CHANGE(StateDuration.TRANSIENT){
               void next(){
                    if(amount>0){
                         println("Your change: "+amount);
                         amount = 0;
                    }
                    state = RESTING;
               }
          },
          TERMINAL{ void output() { println("Halted"); }};
          private boolean isTransient = false;
          State() {}
          State(StateDuration trans) { isTransient = true; }
          void next(Input input){
               throw new RuntimeException("Only call next() for "+
                    "StateDuration.TRANSIENT states");
          }
          void next(){
               throw new RuntimeException("Only call "+
                    "next(Input input) for non-transient states");
          }
          void output() { println(amount); }
     }
     static void run(Generator<Input> gen){
          while(state != State.TERMINAL){
               state.next(gen.next());
               while(state.isTransient)
                    state.next();
               state.output();
          }
     }
     public static void main(String[] args){
          Generator<Input> gen = new RandomInputGenerator();
          if(args.length ==1)
               gen = new FileInputGenerator(args[0]);
          run(gen);
     }
}

class RandomInputGenerator implements Generator<Input>{
     public Input next() { return Input.randomSelection(); }
}
class FileInputGenerator implements Generator<Input>{
     private Iterator<String> input;
     public FileInputGenerator(String fileName){
          input = new TextFile(fileName, ";").iterator();
     }
     public Input next() {
          if(!input.hasNext())
               return null;
          return Enum.valueOf(Input.class, input.next().trim());
     }
}
{% endhighlight %}

###多路分发

Java只支持**单路分发**，即如果要执行的操作包含了不止一个类型未知的对象时，那么java的动态绑定机制只能处理其中一个的类型。要实现**多路分发**需要一些额外的工作，由于多态只能发生在方法调用时，所以要使用多路分发，那么就必须有多个方法调用，每个方法确定一个位置类型。但是一般可以设定一些配置，使一个方法的调用可以引出多个方法的调用。可以使用多种方法实现多路分发。
{% highlight java linenos %}
import static com.mceiba.util.Print.*;
import com.mceiba.util.Enums;
import java.util.*;
enum Outcome { WIN, LOSE, DRAW }
interface Item{
     Outcome compete(Item it);
     Outcome eval(Paper p);
     Outcome eval(Scissors s);
     Outcome eval(Rock r);
}
class Paper implements Item{
     public Outcome compete(Item it) { return it.eval(this); }
     public Outcome eval(Paper p) { return Outcome.DRAW; }
     public Outcome eval(Scissors s) { return Outcome.WIN; }
     public Outcome eval(Rock r) { return Outcome.LOSE; }
     public String toString() { return "Paper"; }
}
class Scissors implements Item{
     public Outcome compete(Item it) { return it.eval(this); }
     public Outcome eval(Paper p) { return Outcome.LOSE; }
     public Outcome eval(Scissors s) { return Outcome.DRAW; }
     public Outcome eval(Rock r) { return Outcome.WIN; }
     public String toString() { return "Scissors"; }
}
class Rock implements Item{
     public Outcome compete(Item it) { return it.eval(this); }
     public Outcome eval(Paper p) { return Outcome.WIN; }
     public Outcome eval(Scissors s) { return Outcome.LOSE; }
     public Outcome eval(Rock r) { return Outcome.DRAW; }
     public String toString() { return "Rock"; }
}
class RoShamBo1{
     static final int SIZE = 10;
     private static Random rand = new Random();
     public static Item newItem() {
          switch(rand.nextInt(3)){
               default:
               case 0: return new Scissors();
               case 1: return new Paper();
               case 2: return new Rock();
          }
     }
     public static void match(Item a, Item b){
          println(a+" vs "+b+": "+a.compete(b));
     }
     public static void test(){
          println("***(1) use same interface ***");
          for(int i=0; i<SIZE; i++)
               match(newItem(), newItem());
     }
}
interface Competitor<T extends Competitor<T>>{
     Outcome compete(T competitor);
}
enum RoShamBo2 implements Competitor<RoShamBo2>{
     PAPER(Outcome.DRAW, Outcome.LOSE, Outcome.WIN),
     SCISSORS(Outcome.DRAW, Outcome.LOSE, Outcome.WIN),
     ROCK(Outcome.DRAW, Outcome.LOSE, Outcome.WIN);
     private Outcome vPAPER, vSCISSORS, vROCK;
     RoShamBo2(Outcome paper, Outcome scissors, Outcome rock){
          this.vPAPER = paper;
          this.vSCISSORS = scissors;
          this.vROCK = rock;
     }
     public Outcome compete(RoShamBo2 it){
          switch(it){
               default:
               case PAPER: return vPAPER;
               case SCISSORS: return vSCISSORS;
               case ROCK: return vROCK;
          }
     }
}
enum RoShamBo3 implements Competitor<RoShamBo3>{
     ROCK{
          public Outcome compete(RoShamBo3 opponent){
               return compete(SCISSORS, opponent);
          }
     },
     SCISSORS{
          public Outcome compete(RoShamBo3 opponent){
               return compete(ROCK, opponent);
          }
     },
     PAPER{
          public Outcome compete(RoShamBo3 opponent){
               return compete(SCISSORS, opponent);
          }
     };
     Outcome compete(RoShamBo3 loser, RoShamBo3 opponent){
          return ((opponent == this) ? Outcome.DRAW :
               ((opponent == loser) ? Outcome.WIN : Outcome.LOSE));
     }
}
enum RoShamBo4 implements Competitor<RoShamBo4>{
     PAPER, SCISSORS, ROCK;
     static EnumMap<RoShamBo4, EnumMap<RoShamBo4, Outcome>> table =
          new EnumMap <RoShamBo4, EnumMap<RoShamBo4, Outcome>>(RoShamBo4.class);
     static{
          for(RoShamBo4 it : RoShamBo4.values())
               table.put(it, new EnumMap<RoShamBo4, Outcome>(RoShamBo4.class));
          initRow(PAPER, Outcome.DRAW, Outcome.LOSE, Outcome.WIN);
          initRow(SCISSORS, Outcome.WIN, Outcome.DRAW, Outcome.LOSE);
          initRow(ROCK, Outcome.LOSE, Outcome.WIN, Outcome.DRAW);
     }
     static void initRow(RoShamBo4 it, Outcome vPAPER, Outcome vSCISSORS, Outcome vROCK){
          EnumMap<RoShamBo4, Outcome> row = RoShamBo4.table.get(it);
          row.put(RoShamBo4.PAPER, vPAPER);
          row.put(RoShamBo4.SCISSORS, vSCISSORS);
          row.put(RoShamBo4.ROCK, vROCK);
     }
     public Outcome compete(RoShamBo4 it){
          return table.get(this).get(it);
     }
}
enum RoShamBo5 implements Competitor<RoShamBo5>{
     PAPER, SCISSORS, ROCK;
     private static Outcome[][] table = {
          { Outcome.DRAW, Outcome.LOSE, Outcome.WIN },
          { Outcome.WIN, Outcome.DRAW, Outcome.LOSE },
          { Outcome.LOSE, Outcome.WIN, Outcome.DRAW }
     };
     public Outcome compete(RoShamBo5 other){
          return table[this.ordinal()][other.ordinal()];
     }
}
public class RoShamBo{
     public static <T extends Competitor<T>> void match(T a ,T b){
          println(a+" vs "+b+": "+a.compete(b));
     }
     public static <T extends Enum<T> & Competitor<T>> void test(Class<T> rsb, int size){
          for(int i=0; i<size; i++)
               match(Enums.random(rsb), Enums.random(rsb));
     }
     public static void main(String[] args){
          RoShamBo1.test();
          println("\n*** (2) use constructorv init instances ***");
          test(RoShamBo2.class, 10);
          println("\n*** (3) use constants method ***");
          test(RoShamBo3.class, 10);
          println("\n*** (4) use EnumMap ***");
          test(RoShamBo4.class, 10);
          println("\n*** (5) use 2D Array ***");
          test(RoShamBo5.class, 10);
     }
}
{% endhighlight %}

##第20章 注解

##第21章 并发

**并发**编程可以使程序执行速度得到极大的提高，或者设计某些类型的程序提供更易用的模型，用并发解决的问题大体上可以分为“速度”和“设计可管理性”两种。

并发是用于多处理器编程额基本工具，但是并发通常是提高运行在单处理器上程序的性能。从性能上说，如果没有**阻塞**，那么在单核处理器上使用并发就没有任何意义，在单核处理系统中性能提高的常见示例是**事件驱动编程**。

###基本的线程机制

实现并发最直接的方式是在操作系统级别使用进程。**进程**是运行在它自己的地址空间内的自包容程序。java提供了**线程**的支持，与多任务操作系统中分叉外部进程不同，线程机制是由执行程序表示的单一进程中创建任务。java的线程机制是抢占式的，调度机制会周期性的中断线程，将上下文切换到另一个线程，从而为每个线程都分配数量合理的时间片去驱动它的任务。单个进程可以有多个并发执行的任务，线程的一大好处是代码不必知道他是运行在一个还是多个CPU的机器上。

多任务和多线程是使用多处理器系统最合理的方式。线程可以驱动任务，Runnable接口的`run()`方法提供了对任务的一种描述方式，将Runnable对象转变为工作任务的传统方式是把它交给一个Thread构造器，`start()`方法进程执行必须的初始化操作，然后调用Runnable对象的`run()`方法，并迅速返回，`start()`方法调用完成之后，执行线程会继续存在，而Thread对象在任务退出`run()`方法死亡之前，GC都不能清理它。调用`Thread.yield()`是对**线程调度器**的一种建议，声明现在可以切换到其他任务。


<div class="alert alert-block alert-warn form-inline" style="text-align:center; vertical-align:middle; font-size: 16px; font-weight:300;">To be continue!</div>