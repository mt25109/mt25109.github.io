---
layout: post
title:  "《Java JDK 学习笔记》读书笔记"
date:   2019-06-04 23:37:24 +0800
categories: jekyll update
---

### Chapter 1 Java平台概论

Java开发者版本一开始是以Java Development Kit名称发表，简称JDK，而J2SE则是平台名称，包含了JDK与Java程序语言。
 Java从1.5开始名称改为5.0，而至6.0后开始称为Java Platform, Standard Edition 6，简称Java SE 6, JDK6全名称为Java SE Development Kit 6。
 版本号6或1.6.0分别表示产品版本（Product version），而1.6.0是开发者版本（Developer version）。
 JDK本身逢带一个JRE，称为Private JRE。Private JRE比Public JRE多了一个server的VM（Virtual Machine）执行选项。

### Chapter 2 从JDK到IDE

一个.java文档可以定义多个类，但是只能有一个公开(public)类，而且主文件名必须与public类名一致。

### 环境变量

PATH环境变量，用于指定javac等命令的位置，便于操作系统在任何位置都可以找到这些命令。
 CLASSPATH环境变量用于设置.class文件的查找路径。

### 命令参数

要在执行程序时指定.class的位置，可以使用-classpath参数进行指定。此参数有缩写形式-cp。如有多个路径信息则用分号分隔。
>  java -cp c:\workspace;c:\classes HelloWorld

当不指定classpath时，默认搜索当前路径，但如果设置了-classpath，那么以classpath参数指定的路径为准。如希望同时搜索当前路径，需要在classpath中以.指定。
>  java -cp .;c:\workspace HelloWorld

如果使用了Java连接库（JAR），只需将JAR当作特别的文件夹即可。
>  java -cp c:\workspace;c:\lib\xyz.jar HelloWorld

在执行java时，如果没有使用-cp或-classpath，就会读取CLASSPATH环境变量。
 同时从Java 6开始，可以使用*表示使用文件夹中所有的JAR。
 当编译java程序时（javac），如果用到了其它类链接库（import），那么也需要指定CLASSPATH.

在编译程序时，可以通过指定-sourcepath来指定原文件所处的位置，这对于工程中有许多package时将很有用。同时还可以通过-d参数来定指class文件的存放位置，使class与java文件分离，便于管理。
>  javac -sourcepath src -d classes src/Main.java

注：在编译时可以通过指定-verbose参数，这样可以看到编译的整个过程，有助于理解。同时-cp参数同样适用于javac命令。

### Package

package用于分类管理不同功能的代码，区分命令空间。

    package com.pa.application.util;
    
    public class Tools{
         //....
    }

当使用package进行分类管理时，它有几点要求与意义：

* 原码文件（.java）必须放置在与package定义名称层级相同的文件夹中。

* package所定义的名称与class名会结合成类的完全吻合名称(Fully qualified name)

* 位码文件（.class）要放置在与package所定义名称层相同的文件夹中。

* 包间可以直接调用的类或方法必须声明为public。

* 编译生成的class文件不需要手动建立文件夹，会自动生成。
>  javac -sourcepath src -cp classes -d classes src/cc/openhome/Main.java
 java -cp classes **cc.openhome.Main**

注：当执行程序时，也需要指定完全吻合名称(fully qualified name)

在执行java命令时，可以附带一个-version变量显示执行的JRE版本。在编译的时候可以使用-target指定编译出的位码必须符合指定平台所允许的版本号。使用-source要求编译程序检查使用的语法不能超过指定的版本。

### Chapter 3 基础语法

如果在指定变量值后不想改变变量的值，可以在声明变量时加上final限定。
 从Java SE7开始，整数或浮点数常量可以用下线线更清楚的表示某些数字。
>  int number1 = 1234_5678;
 double number2 = 3.141_592_636;

移位运算中的左移功能会将所有位往左移动指定位数，左边被挤出的位会被丢弃，右边补上0。
 右移运算符会将所有位往右移指定位数，右边被挤出的位会被丢弃，左边补上原来的位，即左边原来是1就补1，0就补0。
 另外有一个单独的>>>运算符，这个运算符在右移后最左边一定补上0。
 break和continue还可以配合标签使用。

    back:{
        for(int i = 0;i < 10; i++){
            if(i == 9){
                System.out.println("break");
                break back;
            }
        }
        System.out.println("test");
    }

这里程序执行的结果会在break back后，不执行System.out.println语句。
 continue也有类似用法，只不过标签只能设定在for之前。

    back1:
        for(int i = 0; i < 10; i++){
            back2:
            for(int j=0; j < 10; j++){
                if(j == 9){
                    continue back1;
                }
            }
            System.out.println("test");
        }

注：在format字符串时，使用%n换行，此参数可以跨平台。

### Chapter 4 认识对象

基本打包类型中有一种更一般化的Number类，可以用于多种数字类型中。
 自动装箱实际上是一种语法糖用法，它最终在编译后会被展开为：
>  Integer i = 100;
 Integer i = Integer.valueOf(100);

打包器类型因为是一种类类型，故无法用==进行大小的比较。因为这种比较最终比较得是两者的引用地址，而不是值。

    Integer i1 = 200;
    Integer i2 = 200;
    if(i1 == i2){
        System.out.println("i1 == i2");
    }else{
        System.out.println("i1 != i2");
    }

像上例实际上比较的就是引用，而不是值。但有一点值得特别注意。即上例中，将i1和i2的值改为128以下时，比较结果会是相等。这是因为Integer.valueOf()的实现中，对于-128至127之间的值进行缓存。所以当i1和i2在这个区间中赋值时，只是从将引用指向缓存中的地址，这时两者的引用地址相等。
 缓存的数值区别可以通过启动JVM时指定参数java.lang.Integer.IntegerCache.high来设定，但只能设置上界，而不能设置下界。
 数组的建立方法如下，其中第二种方法完全是为了兼容C++的写法，第一种方法更有可读性。因为可以把数组作为一种新的类型来理解。同时数组的索引从0开始计数。
>  int[] scores = {88, 77, 68};
 int scores[] = {88,77, 69};

对于两维数组来说，实际上可以理解为一个保存了数组类型的数组，所以多维数组不并一定都是矩阵，实际上第二维的每一组长度都可以是不相等的。
 数组的建立后，每个元素实际上也都有默认值（0、0.0、null等）。初始值也可以通过Arrays.fill()方法进行填充。
 如果想对数组进行复制操作，可以使用Arrays.copyOf()方法。
 然后注意数组实际上也是一种类类型，所以要注意区分浅层复制(Shallow Copy)和深层复制(Deep Copy)。
 字符串类型需要注意的是，每一次用+进行字符串拼接都会产生临时对象，故拼接如果是一次性的。那么对性能的影响不大。反之，如果拼接产生大量的临时对象，比如在for中进行拼接，那么对性能影响会很大。这时可以直接使用StringBuilder，对于+的拼接，实际上最终也是使用StringBuilder进行的。
 另外需要注意的是StringBuilder是非线程安全的，如果需要线程安全，推荐使用StringBuffer。
 如果字符串常量对字符串变量进行赋值。那么相同的字符串常量产生的变量的地址都是相同的，这是因为编译优化造成的(JVM会建立一个字符串池来维护这些常量信息)。
>  String name1 = “123456”;
 String name2 = “123456”;
 System.out.println(name1 == name2); // true

### Chapter 5 对象封装

在定义类时，可以对成员进行初始化。例如：

    public class Some{
        private int a = 10; //指定初始值
        private String text; //还是有默认值，只是为null
        public Some(int a, String text){
            this.a = a;
            this.text = text;
        }
    }

这样的类在通过构造传参new时，数据成功会初始化两次。第一次初始化为初值，之后经由构造函数再次赋值。
 在Java中，this()代表了调用另一个构造函数，至于调用哪个构造函数，则视调用this()时给的变量类型与个数而定。
 在创建对象之后，调用构造函数之前，若有想执行的流程，可以使用{}定义。

    class Other{
        {
            System.out.println("对象初始化区块");
        }
        Other(){
            System.out.println("Ohter() 构造函数")；
        }
        Other(int o){
            this();
            System.out.println("Other(int 0) 构造函数");
        }
    }
    
    public class ObjectInitalBlock{
        public static void main(String[] args){
           new Other(1);
        }
    }

执行结果：
>  对象初始化区块
 Other()构造函数
 Ohter(int o)构造函数

如果对成员指定final，那么表示延迟对对象成员值的指定，可以在构造函数中执行指定动作，否则编译报错。
 如果有些动作想在位码加载后执行，可以定义static区块。例如：

    class Ball{
        static{
            System.out.println("位码加载后就会被执行")；
        }
    }

在这个例子中，Ball.class加载JVM后，默认就会执行static区块。
 可以用import static语法导入static的成员，在写代码时会省力些。
 不定长变量例表可以简单的用...来定义，可参见下例：

    public class MathTool{
        public static int sum(int... numbers){
            int sum = 0;
            for(int number : numbers){
                sum += number;
            }
            return sum;
        }
        
        public static void main(String[] args){
            System.out.println(MathTool.sum(1, 2, 3));
        }
    }

这种不定长变量列表最终实际上还是语法糖。入参被展开为sum(int ai[]),而传参被展开为sum(new int[]{1, 2, 3})。

### Chapter 6 继承与多态

注解(Annotation)中@Override的作中是要求编译器检查，该方法是否真的重新定义了父类中的某个方法，如果不是的话就引发编译错误。
 抽象方法的定义使用关键字abstract，同时内含抽象方法的类也需要使用abstract关键字修饰。并且抽象类是不可以用来生成实例的。
 子类如果继承抽象类，那于抽象方法有两种做法，一种是继续标示该方法为abstract，另一种是实现此抽象方法。如果两种方法都没有做，那么会引发编译错误。
 **属性的权限有一种不指定权限关键字，这时成员在类的内部，以及相同包的类中都可以存取。**
 如果想取得父类中的方法定义，在调用的父类方法前加上super关键字。

    public class Magican extends Role{
        ...
        @Override
        public String toString(){
            return "魔法师 " + super.toString();
        }
    }

对于在子类中打算重载的方法，其方法的权限定义只能扩大，不能缩小。即如果父类中的同名方法已经声明为public，那么在子类中重新定义时不可以定义为private或protected。
 同时如果重新定义的方法时，返回类型为父类方法返回类型的子类，也是可以的。比如：

    public class Bird{
        protected String name;
        public Bird(String name){
            this.name = name;
        }
        public Bird copy(){
            return new Bird(name);
        }
    }
    //succeed
    public class Chicken extends Bird{
        public Chicken copy(){
            return Chicken(name);
        }

this()和super()在调用时，只能择一调用，而且一定要在构造函数的第一行执行。
 class前也可以加上final关键字，这时表示这个类不能再有子类，即不能被继承。
 成员方法也可以添加final关键字，表示子类不能重载此方法。
 对于自定义的类，如果没有重载equals方法，那么使用时的效果与==相同。

### Chapter 7 接口与多态

接口在被implements时，类也可以延迟实现，等待子类实现。这时的操作方法与抽象类一致，使用abstract。
 继承代表了一种从属关系，而接口表示某种行为的拥有。
 定义接口方法时，如不指定访问权限，默认就是public abstract，也是接口唯一可以指定的访问权限。

    public interface Swimmer{
       void swim(); //默认就是public abstract
    }

接口中可以定义常数，也只能定义public static final修饰的枚举常量。

    public interfcae Action{
        public static final int STOP = 0;
        public static final int RUN = 1;
        //...
    }
    
    public interface Code{
       int SUCCEED = 0;
       int ERROR = 1; //会帮你展开为public static final
    }

### Chapter 8 异常处理

如果某个方法声明会抛出Throwable、Exception或子类实例，但又不属于java.lang.RuntimeException或其子类实例，就必须明确使用try、catch语法加以处理，或者用throws声明这个方法会抛出异常，否则会编译失败。
 Exception或其子类对象，但非属于RuntimeException或其子对象，称为**受检异常**。受编译程序检查。受检异常存在的目的是在于API设计者认定，调用这个方法时，出错机会很高，因此要求编译程序协助（或提醒）调用API的用户明确使用语法处理，**你无权选择要不要处理**。
 属于RuntimeException衍生出来的类实例，通常是事先无法预测错误是否发生的执行时期异常，编译程序不会强迫一定得在语法上加以处理，称为**非受检异常**。
 从JDK7开始，可以使用多重捕捉(multi-catch)语法：

    try{
        ...
    }catch(IOException | InterruptedException | ClassCastException e）{
        e.printStatckTrace();
    }

上述使用方法仍得注意异常的继承。catch括号中，左边的异常不得是右边异常的父类型，否则会发编译错误。
 断言assert的使用时机，一个是对内部不变量的判断，也就是某个时间点上断言某个变量必须是或不是某值。另一个命名用断言的时间为控制流程不变量。
 默认执行时是不启动断言检查的，如果要启动的话，可以在执行java指令时指定-enableassertions或-ea参数。
 如果流程中有return，也有finally区块，那finally区块先执行完后，再将值返回。
 自动尝试关闭资源也是一种语法糖：

    public class FileUtil{
        public static String readFile(String name) throws FileNotFoundException{
            StringBuilder builder = new StringBuilder();
            try(Scanner scanner = new Scanner(new FileInputStream(name))){
                 while(scanner.hasNext()){
                    builder.append(scanner.nextLine());
                    builder.append('\n');
                 }
            }
            return builder.toString();
        }
    }

如示例，想要尝试自动关闭资源的对象，是撰写在try之后的括号里。
 JDK7的尝试自动关闭资源语法可套用的对象，必须实现了java.lang.AutoCloseable接口。
 尝试关闭资源语法也可以同时关闭两个以上的对象资源，只要中间以分号分隔。**并且越后面撰写的对象资源会越早被关闭。**

### Chapter 9 Collection和Map

Queue的offer()、poll()、peek()方法功能上与add()、remove()、element()类似。但最主要的区别在于前三者失败会返回特定值，而后两者会抛出异常。
 offer()成功会返回true，失败返回false。poll()成功会取出队列前端对象，若队列为空则返回null。peek()用来取得（不取出）对象，若队列为空则返回null。
 如果需要迭代对象，可以转换为Iterable对象来提升通用性。此对象包括iterator()方法，用于迭代。
 Collections包括sort()方法，可用于排序，但要求传入的入参是可排序的，即必须实现java.lang.Comparable接口。此接口包括compareTo()方法需要自行实现。
 Collections的sort()方法还有一个重载版本，排序方式将根据Comparator的compare()定义来决定：

    class StringComparator implements Comparator{
        @Override
        public int compare(Object o1, Object o2){
            String str1 = (String) o1;
            String str2 = (String) o2;
            return -str.compareTo(str2);
        }
    }
    
    public class Sort5{
        public static void main(String[] args){
            List words = Arrays.asList("B", "C", "A", "E", "T", "D");
            Collections.sort(words, new StringComarator());
            System.out.println(words);
        }
    }

对于泛型数据类型，从JDK7之后可以简化输入。
>  List words = new LinkedList();
 List words = new ListedList<>(); //JDK7

对于Map对象，如果想同时取得Key与Value，可以使用entrySet()方法。

### Chapter 10 输入/输出

ObjectInputStream提供readObject()方法将数据读入为对象，而ObjectOutputStream提供WriteObject()方法将对象写至目的地，可以被这两个方法处理的对象，必须操作java.io.Serializable接口，这个接口并没有定义任何方法，只是作为标示之用，表示这个对象是可以串行化的（Serializable）。
 如果在做对象串行化时，对象中某些数据成员不希望被写出，则可以标上transient关键字。

### Chapter 11 线程与并行API

操作线程有两种方法，单独定义行为在Runnable类中，或者直接继承Thread类。但Runnable的弹性更好。
 如果一个Thread被标示为Daemon线程，在所有非Daemon线程都结束时，JVM自动就会终止。从main()方法开始的就是一个非Daemon线程，可以使用setDaemon()方法来设定一个线程是否为Daemon线程。
 同时，由Daemon线程产生的子线程也将会是Daemon线程。
 一个进入Blocked状态的线程，可以由另一个线程调用该线程的interrupt()方法，让它离开Blocked状态。此时线程会抛出InterruptedException异常对象，这是让线程“醒过来”的方式。

    public class InterruptedDemo{
        public static void main(String[] args){
            Thread thread = new Thread(){
                @Override
                public void run(){
                    try{
                        Thread.sleep(9999);
                    }catch(InterruptedException ex){
                        System.out.println("I am wake up.");
                    }
                }
            };
            thread.start();
            thread.interrupt(); //主线程调用thread的interrupt()

如果希望等待某个线程执行结束后再继续执行，可以调用线程的join()方法。

    public class JoinDemo{
        public static void main(String[] args){
            System.out.println("Main thread 开始...");
            Thread threadB = new Thread(){
                @Override
                public void run(){
                    try{
                        System.out.println("Thread B 开始...");
                        for(int i =0; i < 5; i++){
                            Thread.sleep(1000);
                            System.out.println("Thread B 执行...");
                        }
                        System.out.println("Thread B 将结束...");
                    }catch(InterruptedException e){
                        e.printStackTrace();
                    }
                }
            };
            threadB.start();
            try{
                //Thread B 加入Main thread流程
                thread.join();
            }catch(InterruptedException e){
                e.printStackTrace();
            }
            System.out.println("Main thread将结束...");
        }
    }

在Main线程执行过程中，创建了threadB线程并执行。使用join()方法，让主线程等待threadB执行完毕，主线程才会继续走完后续流程。
 有时候加入的线程可能处理太久，你不想无止境等待这个线程工作完毕，则可以在join()时指定时间。如join(10000)表示等待最多10000毫秒，也就是10秒。
 Thread类还有一个stop()方法，但已经被标为Deprecated，表示此方法已经不推荐使用了。除此之外，resume()、suspend()、destroy()等方法也不建议继续使用。
 要停止线程，最好让线程跑完应有的流程。例如线程在无穷循环中进行某个运作，那么停止线程的方式就是让它有机会离开无穷循环。
 为了保证函数在线程中的原子性，可以使用synchronized修辞方法，使得线程在方法结束前，不会被CPU轮转。同时synchronized不只可以修辞方法，也可以描述句方式使用。synchronized的实现方式是控制对的锁。

    public void add(object o){
        synchronized(this){
            if(next == list.length){
                list = Arrays.copyOf(list, list.length * 2);
            }
            list[next++] = o;
        }
    }

同时举一反三，由于这个语法可以指定锁定的对象，所以还可以这么写。

    ...
    final ArrayList list = new ArrayList();
    Thread t1 = new Thread(){
        public void run(){
            while(true){
                synchronized(list){
                    list.add(1);
                }
            }
        }
    };
    Thread t2 = new Thread(){
        public void run(){
            while(true){
                synchronized(list){
                    list.add(2);
                }
            }
        }
    };
    ...

这时必须取得list对象的锁，才能执行list.add()方法。
 对于Collection和Map对象，可以命名用Collections的synchronizedCollection()、synchronizedList()、synchronizedSet()、synchronizedMap()等方法，这些方法会将传入的Collection、List、Set、Map操作对象打包，返回线程安全的对象。
 被标示为volatile的变量，不充许线程快取，变量值的存取一定是在共享内存中进行。
 wait()、notify()、notifyAll()是三个控制线程释放对象锁、通知线程参与锁竞争。他们之间的运转状态图如下：


 从JDK5开始提供了java.util.concurrent包，可以基于其中的API建立稳固的并行应用程序，而不用亲自处理synchronized、wait()、notify()、notifyAll()等细节。
 Lock接口提供了类似于synchronized的功能（实现可以用ReentrantLock），并定义了tryLock()方法，如果没有取得锁也不会发生阻断，而是返回false，线程可以继续执行。
 ReadWriteLock接口可以理解为多重读，独占写接口，其实现类为ReentrantReadWriteLock。
 Condition用于取得类似于wait()、notify()、notifyAll()的效果。可调用Lock的newCondition()来取得Condition对象。
 为了分离Runnable与实际执行，JDK5开始定义了java.util.concurrent.Executor接口。Executor接口只定义了一个execute()方法：

    public interface Executor{
        void execute(Runnable command);
    }

通过例子来看一下分离是如何做到的。

    import java.net.URL;
    import java.util.concurrent.*;
    import java.io.*;
    
    public class Pages{
        private URL[] urls;
        private String[] fileNames;
        private Executor executor;
        
        public Pages(URL[] urls, String[] fileNames, Executor executor){
            this.urls = urls;
            this.fileNames = fileNames;
            this.executor = executor;
        }
        
        public void download(){
            for(int i = 0; i < urls.length; i++){
                final int index = i;
                executor.execute(new Runnable(){
                    @Override
                    public void run(){
                        try{
                            dump(urls[index].openStream(),
                                 new FileOutputStream(fileNames[index]));
                        }catch(IOException ex){
                            throw new RuntimeException(ex);
                        }
                    }
                });
            }
        }
        
        private void dump(InputStream src, OutputStream dest) throw IOException{
            try(InputStream input = src; OutputStream output = dest){
                byte[] data = new byte[1024];
                int length = -1;
                while((length = input.read(data)) != -1){
                    output.write(data, 0, length);
                }
            }
        }
    }

直接下载任务，就可以这样使用：

    public class DirectExecutor implements Executor{
        public void execute(Runnable r){
            r.run();
        }
    }

使用线程下载任务：

    public class ThreadPerTaskExecutor implements Executor{
        public void executor(Runnable r){
            new Thread(r).start();
        }
    }

两者的使用也很简单：

    public class Download{
        public static void main(String[] args) throws Exception{
            URL[] urls = {
                new URL("www.baidu.com"),
                new URL("www.sina.com.cn"),
                new URL("www.163.com"),
                new URL("www.weibo.com")
            };
            String fileNames = {
                "baidu.html",
                "sina.html",
                "163.html",
                "weibo.html"
            };
            //直接下载
            new Pages(urls, fileNames, new DirectExecutor()).download();
            //线程下载
            new Pages(urls, fileNames, new ThreadPerTaskExecutor()).download();
        }
    }

如需要支持线程池的Executor，JAVA SE API中已经提供了java.util.concurrent.ThreadPoolExecutor支持。根据不同线程池的需求，可以使用此类的newCachedThreadPool()和newFixedThreadPool()静态方法来创建ThreadPoolExecutor实例。前者实现在必要时建立线程，后者建立固定数量的线程。
>  这两个方法也有接受java.util.concurrent.ThreadFactory版本，可以在ThreadFactory的newThread()方法中，操作如何建立Thread实例。

使用ThreadPoolExecutor搭配前面Pages使用的例子：

    public class Download{
        public static void main(String[] args) throws Exception{
            ...
            ExecutorService executorService = Executors.newCachedThreadPool();
            new Pages(urls, fileNames, executorService).download();
            executorService.shutdown();
        }
    }

ExecutorService的shutdown()方法会在指定执行的Runnable都完成后关闭ExecutorService。还有一个shutdownNow()，则可以立即关闭，同时尚末执行的Runnable对象会以List返回。
 对于异步操作，有Future类可以操作，其经常与Callable接口搭配使用，Callable与Runable类似，但Callable可以有返回值，也可以抛出异常。同时由于Future也操作了Runnable接口，所以可以指定给Thread创建之用。

    import java.util.concurrent.*;
    public class FutureCallableDemo{
        public static long fibonacci(long n){
            if(n <= 1){
                return n;
            }else{
                return (fibonacci(n-1) + fibonacci(n-2));
            }
        }
        
        public static void main(String[] args){
            FutureTask<Long> fib30 = new FutureTask<>（
                new Callable<Long>(){
                    public Long call(){
                        return fibonacci(30);
                    }
                }
            );
            new Thread(fib30).start();
            try{
                Thread.sleep(5000);
                System.out.printf("第30个费式数:%d%n", fib30.get());
            }catch(InterruptedException | ExecutionException ex){
                ex.printStackTrace();
            }
        }
    }

也可以使用ExecutorService的submit()方法，它可以接受Callable对象，返回Future对象。重新改写上面的例子：

    ...
    public static void main(String[] args){
        ExecutorService service = Executors.newCachedThreadPool();
        Future<Long> fib30 = service.submit(new Callable<Long>(){
            public Long call(){
                return fibonacci(30);
            }
        });
        try{
            Thread.sleep(5000);
            System.out.printf("第30个费式数:%d%n", fib30.get());
        }catch(InterruptedException | ExecutionException ex){
            ex.printStackTrace();
        }    
    }
    ...

如果有多个Callable，可以先收集到Collection中，然后调用ExecutorService的invokeAll()，这会以List<Futrue<T>>返回与Callable相关的Future对象。如果多个Callable，只要有一个执行完成就可以，那么调用ExecutorService的invokeAny()即可。

ScheduledExecutorService是ExecutorService的子接口，可以让你进行工作排程。schedue()方法用来排定Runnable或Callable实例延迟多久后执行一次，并返回Future子接口ScheduledFuture的实例。对于需要重复性执行的工作，可以使用scheduleWithFixedDelay()与scheduleAtFixedRate()方法。
 scheduleWithFixedDelay()方法是指在执行完Runnable后延迟多久后再次执行。
 scheduleAtFiexedRate()方法是指按约定的周期执行Runnable，如果前一次Runnable未超过指定的周期，那么就会按排定时间执行下一次。如果前一次Runnable执行时长超过了下一次指定的周期，那么下一次执行会在前一次执行完成后立即执行。
 同时两者执行任务时抛出的异常都不会影响下一次的排程执行。
 ScheduledExecutorService的操作类ScheduledThreadPoolExecutor为 ThreadPoolExecutor的子类，具有线程池与排程功能。可以使用Executors的 newScheduledThreadPool()方法指定返回内建多少个线程的 ScheduledThreadPoolExecutor；使用newSingleThreadScheduledExecutor() 则可使用单个线程执行排定的工作。

    import java.util.concurrent.*;
    
    public class ScheduleExecutorServiceDemo{
        public static void main(String[] args){
            ScheduleExecutorService service = Executors.newSingleThreadScheduleExecutor();
            service.scheduleWithFixedDelay(
                new Runnable(){
                    public void run(){
                        System.out.println(new java.util.Date());
                        try{
                            Thread.sleep(2000);
                        }catch(InterruptedException ex){
                            throw new RuntimeException(ex);
                        }
                    }
                }, 2000, 1000, TimeUnit.MILLISECONDS);
        }
    }

Future的另一操作类java.util.concurrent.ForkJoinTask及其子类，与 ExecutorService的另一操作类java.util.concurrent.ForkJoinPool有关，都是主要用来解决分而治之的问题。

### Chapter 12 通用API

### Date、DateFormat、Calender

取得系统时间可以使用System.currentTimeMillis()，返回long类型整数，代表1970年1月1日0时至今的毫秒数。也可以用Date()让这个整数变得有意义一些。
>  Date date = new Date(System.currentTimeMillis());

如果需要格式化输出，可以使用DateFormat，它是一个抽象类。操作类为java.text.SimpleDateFormat。也可以通过DateFormat的getDateInstance()、getTimeInstance()、getDateTimeInstance()等静态方法来获得SimpleDateFormat实例。
 SimpleDateFormat还有一个parse()方法，可以依创建SimpleDateFormat时指定的格式将指定字符串剖析为Date()实例。
 Calendar用于获取比如想知道今天是11月的第几天这种问题。通过Calendar的getInstance()来取得实例。它还有add()方法，可以用于日期计算。

### 规则表达式

规则表达式用于字符串格式验证、分割、指定字符串特定样式字串的统计等等。
 规则表示式中可以使用标点符号字符，如逗号(,)，但有些标点符号在规则表示式中有特别意义， 如!$^*()+={}[]|\:.?等。若要比较这些字符，则必须加上忽略符号，例如要比较!，则必须使用!，要比较$字符，则必须 使用$。 如果不确定哪些标点符号字符要加上忽略符号，可以 在每个标点符号前加上\。
 规则表示式中，多个字符可以分归在一起，成为一个字符类（Character class），字符类会 比较文字中是否有“任一个”字符符合字符类中某个字符。归类字符的方式之一是将字符放于[] 中。
 字符类中可以使用连字符-。例如，要比较文字中是否有1到5的数字，规则表示式为[1-5]；要比较文字中是否有a到z的字母，规则表示式为[a-z]；要比较文字中是否有1到5、a到z、M到W，规则表示式可以写为[1-5a-zM-W]。[]则为反字符类（Negated character class），例如[abc]会比较a、b、c以外的字符。
 如果想让用户输入的手机号码格式为XXXX-XXXXXX，其中X为数字，虽然规则表示式可以使用\d\ d\d\d-\d\d\d\d\d\d，不过更简单的写法是\d{4}-\d{6}。{n}是贪婪量词（Greedy quantifier）表示法的一种，表示前面的项目出现n次。
 如果在贪婪量词表示法后加上?，将会成为逐步量词（Reluctant quantifier），比较器看到逐步量词时，会一边吃掉剩余文字，一边看看吃下的文字是否符合规则表示式，结果就是逐步量词会尽可能地找出长度最短的符合文字。
 如果在贪婪量词表示法后加上+，将会成为独吐量词（Possessive quantifier），比较器看到独吐量词时，会先将剩余文字吃掉，然后看看独吐量词部分是否符合吃下的文字，如果符合就不会再吐出来了。


 如果有文字Justin dog Monica doggie Irene，你想要依当中单字dog切出前后两个子字符串，也就是Justin与Monica doggie Irene两个部分。通过下面的用法是不行的。
>  “Justin dog Monica doggie Irene”.split(“dog”);

可以使用\b标出单词的边界，如\bdog\b，这样就会只比较出dog单词。
 分组回头参考时，是在\后加上分组计数，表示参考第几个分组的比较结果。例如，\d\d要求比较两个数字，(\d\d)\1的话，表示要输入四个数字，输入的前两个数字与后两个数字必须相同，例如输入1212会符合，因为12符合(\d\d)，\1要求接下来输入也要是12；若输入1234则不符合，因为12符合(\d\d)，\1要求接下来的输入也要是12，然而接下来的 数字是34，因而不符合。
 对于需要频繁使用的规则，可以使用java.util.regex.Pattern实例。Pattern的构造函数被标为private，所以无法直接使用new创建，必须通过静态方法compile()来取得。此方法在剖析、验证规则表达式无误后，会返回Pattern的实例供使用。

### NIO2文件系统
>  在JDK7之前，路径指定是使用从JDK1.0开始就存在的java.io.File，这个类功能有限，有着各平台上行为不一致等问题。在JDK7中提供有File转换Path的API。

路径可以使用resolve()结合。
>  Path path1 = Paths.get(“C:\Users”);
 Path path2 = Paths.get(“Justin”);
 Path path3 = path1.resolve(path2);

如果有两个路径，想知道如何从一个路径切换至另一个路径，则可以使用relativize()方法。
 如果想确定Path代表的路径，实际上是否存在文档，可以使用Files.exists()或Files.notExists()。Files.exists()仅在文档存在时返回true，如果文档不存在或无法确认存不存在（例如没有权限存取文档）则返回false。Files.notExists()会在文档不存在时返回true，如果文档存在或无法确认存不存在则返回false。
 如果想在列出目录内容时过滤想显示的文档，例如只想显示.class与.jar文档，可以在使用Files.newDirectoryStream()时的第二个参数指定过滤条件为*.{class,jar}。例如：

    try(DirectoryStream<Path> stream = Files.newDirectoryStream(
                                Paths.get(args[0]), "*.{class,jar}"){
        for(Path entry: stream){
            System.out.println(entry.getFileName());
        }
    }

像\*.{class,jar}这样的语法称为Glob，是一种模式比较语法，比规则表示式简单，常用于 目录与文件名的比较。

### Chapter 14 整合数据库

在生成Connection对象时，除了需要指定用户名和密码外，还需要提供JDBC URL，其定义了连接数据库时的协议、子协议、数据源识别：
>  协议:子协议:数据源识别

除了“协议”在JDBC URL中总是以jdbc开始之外，JDBC URL格式各家数据库都不相同，须查询数据库产品使用手册。如MySQL：
>  jdbc:mysql://主机名:端品/数据库名称?参数=值&参数=值

如果要使用中文存取，还必须给定参数useUnicode及characterEncoding指定字符编码方式。
 有时候会将JDBC URL写到XML配置文件中，此时不能直接在XML中写&符号，必须改为&amp。

### Chapter 15 反射与类加载器

可以利用Class.forName()动态加载.class文档，取得Class对象之后，利用其newInstance()方法建立类实例。
>  Class clz = Class.forName(args[0]);
 Object obj = clz.newInstance();

如果类的构造函数带有参数，那么可以使用如下的方法：
>  Class clz = Class.forName(args[0]);
 Constructor constructor = clz.getConstructor(Integer.TYPE);
 List list = (List) constructor.newInstance(100);

在某些情况下，也许想调用受保护的(protected)或私有(private)方法，可以使用Class的getDeclaredMethod()取得方法，并在调用Method的setAccessible()时指定为true。
>  Method priMth = clz.getDeclaredMethod(“priMth”,…);
 priMth.setAccessible(true);
 priMth.invoke(target, args);

### Chapter 16 自定义泛型、枚举与注解

在定义泛型时，可以定义类型的边界。例如：
>  class Duck<T extends Animal>{}

这里限定了T实际类型必须是Animal的子类。而且不止于此，还可以限定多个条件。
>  public class Some<T extends Iterable<T> & Comparable<T>>

如果B是A的子类，而Node<B>可视为一种Node<A>，则称Node具有共变性(Covariance)或有弹性的(flexible)。Java的泛型并不具有共变性，不过可以使用类型通配字符?与extends来声明变量，使其达到类似共变性。
>  Node<Apple> apple = new Node<>(new Apple(), null);
 Node<? extends Fruit> fruit = apple;

如果B是A的子类，而Node<A>视为一种Node<B>，则称Node具有逆变性(Contravariance)。可以使用类型通配字符?和super来声明，以达到类似逆变性的效果。
>  Node<Fruit> fruit = new Node<>(new Fruit(), null);
 Node<? super Banana> node = fruit;
 Node<? super Apple> node = fruit;
