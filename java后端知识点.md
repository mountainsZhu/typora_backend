

### 静态语言-动态语言

动态语言：可以在运行时改变自身结构，eg：javascript、c#、php、python 

静态语言：运行结构不可改变  eg：java、c、c++。==但是：通过注解+反射，java可以实现准动态。==

#  JVM

JVM是由c、c++、汇编混合编写的；java无法操作内存，都是调用c++操作的内存。

### 堆

存放：new的对象，成员变量（运行时创建）

### 栈

存放：基本类型数据和对象的引用，局部变量。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E6%A0%88%E4%B8%AD%E5%AD%98%E6%94%BE.jpg)



### 方法区

存放：常量数据和.class文件

### new的过程

1.创建对象

2.对象初始化

3.堆内地址放入栈中



### 一些细节

1.对于equals相等的字符串，在常量池中永远只有一份，在堆中有多份。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/String1.PNG)





2.变量和引用存储在栈中，常量存储在常量池中。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/string2.PNG)



3.对于成员变量和局部变量：成员变量就是方法外部，类的内部定义的变量；局部变量就是方法或语句块内部定义的变量。局部变量必须初始化。

形式参数是局部变量，局部变量的数据存在于栈内存中。栈内存中的局部变量随着方法的消失而消失。

成员变量存储在堆中的对象里面，由垃圾回收器负责回收。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/String3.PNG)

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/String3.2.PNG)

**1.main方法开始执行：int date = 9;**

date局部变量，基础类型，引用和值都存在栈中。

**2.Test test = new Test();**

test为对象引用，存在栈中，对象(new Test())存在堆中。

**3.test.change(date);**

i为局部变量，引用和值存在栈中。当方法change执行完成后，i就会从栈中消失。

**4.BirthDate d1= new BirthDate(7,7,1970);**  

d1为对象引用，存在栈中，对象(new BirthDate())存在堆中，其中d，m，y为局部变量存储在栈中，且它们的类型为基础类型，因此它们的数据也存储在栈中。day,month,year为成员变量，它们存储在堆中(new BirthDate()里面)。当BirthDate构造方法执行完之后，d,m,y将从栈中消失。

**5.main方法执行完之后，date变量，test，d1引用将从栈中消失，new Test(),new BirthDate()将等待垃圾回收。**



### 特：引用地址比较（==）

 ```java
String s1 = "Hello";
String s2 = "Hello";
String s3 = "Hel" + "lo";
String s4 = "Hel" + new String("lo");
String s5 = new String("Hello");
String s6 = s5.intern();
String s7 = "H";
String s8 = "ello";
String s9 = s7 + s8;
          
1.System.out.println(s1 == s2);  // true
2.System.out.println(s1 == s3);  // true
3.System.out.println(s1 == s4);  // false
4.System.out.println(s1 == s9);  // false
5.System.out.println(s4 == s5);  // false
6.System.out.println(s1 == s6);  // true
 ```

1相等原因：常量池中若有相同内容，则不会新创建

3不等原因：一常量一堆

4不等原因：s7和s8两变量拼接后地址不定，不可能和是s1相同

5不等原因：两个对象

6相等原因：intern方法会尝试将Hello字符串添加到常量池中，并返回其在常量池中的地址，因为常量池中已经有了Hello字符串，所以intern方法直接返回地址；而s1在编译期就已经指向常量池了，因此s1和s6指向同一地址，相等。



# java基础知识

#### java8大基本类型

8大基本类型及相应字节数

byte 1字节
int 4字节
short 2字节
long 8字节
float 4字节
double 8字节
Boolean 1位
char 2字节

#### 函数式接口

只有一个方法的接口，eg:Runnable接口只有一个run方法

#### λ表达式

```java
//(参数)->{代码块}
package com.redis.springboot.Test;

public class TestTemp {

    public static void main(String[] args) {
        int data = 1;
        //data = 2;---------------------------------------------------------------
        new Thread(()->{
            new func3().print(data);
        },"A").start();
    }
}

class func3{
    public void print(int data){
        System.out.println("this is a data:"+data);
    }
}
```

注意，由于λ表达式属于匿名内部类，会在另一个线程上运行，所以当前线程中的局部变量要在λ表达式的代码块运用则需要定义为final，如果没有自己定义，则编译器编译时候会自动将其定义成final。eg：如果加上上图中注释的那一行则会报错。

#### stream流计算

```java
//在面对大数据计算时，流计算采用分布式并行思想，将计算任务均摊，多核运作，极大的提高运算的实时性和运算效率
public class CalculateMethod {
    //普通方法
    public void normal(){
        long sum=0;
        for(Long i=1L;i<=1_0000_0000L;i++){
            sum+=i;
        }
        System.out.println("结果："+sum);
    }
    //并行流计算
    public void stream(){
        System.out.println("结果："+LongStream.rangeClosed(0L, 1_0000_0000L).parallel().reduce(0, Long::sum));
    }
}

/*
结果：5000000050000000
所花时间：650ms
结果：5000000050000000
所花时间：90ms
*/
```

> 在使用流计算时要注意

1. 留意装箱。自动装箱和拆箱操作会大大降低性能。Java8中有原始类型流（IntStream、LongStream、DoubleStream）来避免这种操作，但凡有可能都应该使用这些原始流。
2. 有些操作本身在并行流上的性能就比顺序流差。特别是limit、findFirst等依赖于元素顺序的操作，它们在并行流上执行的代价非常大。例如，findAny会比findFirst性能好，因为它不一定要顺序来执行。你总是可以调用unordered方法来把顺序流变成无须流。那么，如果你需要流中的n个元素而不是专门的前n个的话，对无序并行流调用limit可能会比单个有序流（比如数据源是List）更高效。
3. 还要考虑流的操作流水线的总计算成本。设N是要处理的元素的总数，Q是一个元素通过流水线的大致处理成本，则N*Q就是这个对成本的一个粗略的定型估计。Q值较高就意味着使用并行流时性能好的可能性比较大。
4. 对于较小的数据量，选择并行流几乎从来不都是一个好的选择。并行处理少数几个元素的好处还抵不上并行化造成的额外开销。
5. 要考虑流背后的数据结构是否易于分解。例如，ArrayList的拆分效率比LinkedList高很多，因为前者用不着遍历就可以平均拆分，而后者则必须遍历。另外，用range工厂方法创建的原始类型流也可以快速分解。最后，你可以自己实现Spliterator来完全掌握分解过程。
6. 流自身的特点，以及流水线中的中间操作修改流的方式，都可能会改变分解过程的性能。例如，一个SIZED流可以分成大小相等的两部分，这样每个部分都可以比较高效地并行处理，但筛选操作可能丢弃的元素个数却无法预测，导致流本身的大小未知。
7. 还要考虑终端操作中合并步骤的代价是大是小（例如Collector中的combiner方法）。如果这一步代价很大，那么组合每个子流产生的部分结果所付出的代价就可能会超出通过并行流得到的性能提升。



#### ForkJoin

> 思想：拆分合并思想

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/ForkJoin%E6%8B%86%E5%88%86%E5%90%88%E5%B9%B6%E6%80%9D%E6%83%B3.PNG)

> 优势：能者多劳、任务窃取

由于ForkJoin维护的是双端队列。当A、B线程同时执行任务，如果A提前完成了任务，而B还没有完成，则A会从B的尾部拿出一部分任务自己执行。

> 具体使用

```java
//递归计算
public class TestForkJoin extends RecursiveTask<Long> {

    Long start = 1L;
    Long end = 10_0000_0000L;
    Long flag = 10000L;

    public TestForkJoin(Long start,Long end){
        this.start =start;
        this.end = end;
    }
    @Override
    protected Long compute() {
        Long result = 0L;
        if(end-start>=flag){//当计算量大于预设值采用forkjoin
            //forkjoin,计算任务一分为二
            Long middle = (start+end)>>1;
            TestForkJoin task1 = new TestForkJoin(start,middle);
            task1.fork();//拆分任务，任务入队
            TestForkJoin task2 = new TestForkJoin(middle+1,end);
            task2.fork();//拆分任务，任务入队

            return task1.join() + task2.join();//join取值
        }else{
            //普通计算
            for (Long i = start; i <= end; i++) {
                result += i;
            }
            return result;
        }
    }
}
```

三种方法调用比较

```java
public class Atest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        Long start = 1L;
        Long end = 10_0000_0000L;
        Long flag = 10000L;

        Long stime = System.currentTimeMillis();

        //一般计算 7660ms 结果 500000000500000000
        //System.out.println(normal(start,end));
        //forkjoin 5470ms 结果 500000000500000000
        //System.out.println(forkjoin(start,end));
        //并行流 360ms  结果500000000500000000
        System.out.println(stream(start,end));

        Long etime = System.currentTimeMillis();
        System.out.println(etime-stime);
    }
    public static Long normal(Long s,Long e){//一般方法
        Long r =0L;
        for (Long i = s; i <= e; i++) {
            r += i;
        }
        return r;
    }
    public static Long forkjoin(Long s,Long e) throws ExecutionException, InterruptedException {//forkjoin
        ForkJoinPool forkJoinPool = new ForkJoinPool();//创建一个forkjoin池
        ForkJoinTask <Long> task = new TestForkJoin(s,e);//创建计算任务
        ForkJoinTask <Long> submit = forkJoinPool.submit(task);//将计算任务提交到池子中
        return submit.get();//获取计算结果
    }
    public static Long stream(Long s,Long e){//并行流，底层是forkjoin
        return LongStream.rangeClosed(s,e).parallel().sum();
    }
}
```







#### 链式编程

```java
/*
利用Array.asList构建list，获取数组的流，再调用流的方法筛选年龄是偶数、小于22且按照名字倒序排序输出第一列数据名字的小写
*/
public class TestLinkedCode {
    public static void main(String[] args) {
        LinkedCodeCommon l1 = new LinkedCodeCommon(1,18,"A");
        LinkedCodeCommon l2 = new LinkedCodeCommon(2,19,"B");
        LinkedCodeCommon l3 = new LinkedCodeCommon(3,20,"C");
        LinkedCodeCommon l4 = new LinkedCodeCommon(4,21,"D");
        LinkedCodeCommon l5 = new LinkedCodeCommon(5,22,"E");
        LinkedCodeCommon l6 = new LinkedCodeCommon(6,23,"F");
        List <LinkedCodeCommon>list = Arrays.asList(l1,l2,l3,l4,l5,l6);
        //一行代码实现5个功能
        list.stream()
                .filter((x)->{return x.getAge()%2==0;})
                .filter((x)->{return x.getAge()<22;})
                .map((x)->{return x.getName().toLowerCase();})
                .sorted((c1,c2)->{return c2.compareTo(c1);})
                .limit(1)
                .forEach(System.out::println);
    }
}
```



### 基本数据类型

int short long bool char byte float double

### 引用数据类型

eg:String

### 面向对象机制



### 反射Reflection

#### Class对象

定义：从某种意义上来说，java有两种对象：实例对象和Class对象。每个类的运行时的**类型信息**就是用Class对象表示的。它包含了与类有关的信息。其实我们的实例对象就通过Class对象来创建的。Java使用Class对象执行其RTTI（运行时类型识别，Run-Time Type Identification），多态是基于RTTI实现的。不能主动创建。

创建：可以通过对象、类名、全限定名、子类class获得

```java
public class testReflection {
    public static void main(String[] args) throws ClassNotFoundException {
        Person person = new Student("张三");
        //通过对象获得
        Class c1 = person.getClass();
        System.out.println(c1.hashCode());
        //通过类名.class
        Class c2 = Student.class;
        System.out.println(c2.hashCode());
        //通过forName根据文件路径获得
        Class c3 = Class.forName("com.redis.springboot.Student  ");
        System.out.println(c3.hashCode());
        //获得某类的父类class
        Class c4 = c1.getSuperclass();
        System.out.println(c4.getName());
    }
}

@Data
@AllArgsConstructor
@NoArgsConstructor
class Person{
    String name;
}

class Student extends Person{
    public Student(String name){
        this.name = name;
    }
}
class Teacher extends Person{
    public Teacher(String name){
        this.name = name;
    }
}
```

一个类被加载到内存并供我们使用需要经历如下三个阶段：

1. **加载**，这是由类加载器（ClassLoader）执行的。通过一个类的全限定名来获取其定义的二进制字节流（Class字节码），将这个字节流所代表的静态存储结构转化为方法去的运行时数据接口，根据字节码在java堆中生成一个代表这个类的java.lang.Class对象。
2. **链接**。在链接阶段将验证Class文件中的字节流包含的信息是否符合当前虚拟机的要求，为静态域分配存储空间并设置类变量的初始值（默认的零值），并且如果必需的话，将常量池中的符号引用转化为直接引用。
3. **初始化**。到了此阶段，才真正开始执行类中定义的java程序代码。用于执行该类的静态初始器和静态初始块，如果该类有父类的话，则优先对其父类进行初始化。

**所有的类都是在对其第一次使用时，动态加载到JVM中的（懒加载），因此java程序程序在它开始运行之前并非被完全加载，其各个类都是在必需时才加载的。·**

**对于同样类型和维度的数组，他们的hashcode是一样的。**



#### 加载器

系统类：AppClassLoader:负责Classpath下类的加载(常用加载器)

拓展类：ExtClassLoader:负责JRE扩展目录ext中类的加载

以上都是ClassLoader的子类

引导类：根加载器:由C++编写，java中看不到，它负责java核心类的加载，如rt.jar中的类

**补：双亲委派机制:加载一个类时会向上寻找其父加载器，如果这个类已经被加载过了则无法再被加载，否则就从根加载器往下找到最低一层的加载器进行加载**



#### 常规反射操作

```java
public class test {
    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException, NoSuchFieldException {

        Class c1 = SingleTest.class;

        System.out.println(c1.getName());//获取SingleTest的包名加类名
        System.out.println(c1.getSimpleName());//获取SingleTest的类名

        Constructor <SingleTest>constructor = c1.getDeclaredConstructor();
        Field fields[] = c1.getFields();//找到所有公有属性
        fields = c1.getDeclaredFields();//找到所有属性
        for (int i = 0; i < fields.length; i++) {
            System.out.println(fields[i]);
        }
        Method methods[] = c1.getMethods();//找到所有公有方法
        methods = c1.getDeclaredMethods();//找到所有方法
        for (int i = 0; i < methods.length; i++) {
            System.out.println(methods[i]);
        }
        constructor.setAccessible(true);//给予构造器调用私有无参构造函数的权限

        //反射创建实例
        SingleTest s1 = constructor.newInstance();
        s1.sayHello();

        //反射调用私有方法
        SingleTest s2 = constructor.newInstance();
        Method temp = c1.getDeclaredMethod("setName", String.class);
        temp.setAccessible(true);//setName是私有方法，需要给予权限
        temp.invoke(s2,"我最牛！");//激活方法
        System.out.println(s2.getName());

        //反射修改私有属性
        SingleTest s3 = constructor.newInstance();
        Field temp2 = c1.getDeclaredField("name");
        temp2.setAccessible(true);//name是私有属性，需要给予权限
        temp2.set(s3,"zss");//设置属性
        System.out.println(s3.getName());
    }
}
```



#### 通过反射获取注解信息

```java
public class Reflection_Annotation{
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        //获取类的注解
        Class c1 = Class.forName("com.redis.springboot.Test.Student1");
        Annotation[] annotation = c1.getAnnotations();
        for (Annotation annotation1 : annotation) {
            System.out.println(annotation1);
        }
        System.out.println("***********************************************************");
        //获取类注解的值value
        Anno1 anno1 = (Anno1) c1.getAnnotation(Anno1.class);
        String value = anno1.value();
        System.out.println(value);
        System.out.println("***********************************************************");
        //获取类中属性的注解
        Field field = c1.getDeclaredField("name");
        Anno2 anno2 = field.getAnnotation(Anno2.class);
        System.out.println(anno2.column()+" "+anno2.type()+" "+anno2.length());
        System.out.println();
    }

}

@NoArgsConstructor
@AllArgsConstructor
@Anno1("ZSS")
class Student1{
    @Anno2(column = "id",type = "varchar(50)",length = 50)
    private String id;
    @Anno2(column = "age",type = "int",length = 10)
    private int age;
    @Anno2(column = "name",type = "varchar(50)",length = 50)
    public String name;
}

//类的注解
@Target(value = ElementType.TYPE)
@Retention(value = RetentionPolicy.RUNTIME)
@interface Anno1{
    String value();
}

//属性的注解
@Target(value = ElementType.FIELD)
@Retention(value = RetentionPolicy.RUNTIME)
@interface Anno2{
    String column();
    String type();
    int length();
}

/*结果
@com.redis.springboot.Test.Anno1(value="ZSS")
***********************************************************
ZSS
***********************************************************
name varchar(50) 50
*/
```





### 注解Annotation

#### 内置注解

- @Deprecated 已过期，表示方法是不被建议使用的
- @Override 重写，标识覆盖它的父类的方法
- @SuppressWarnings 压制警告，抑制警告

#### 元注解

```java
//定义一个注解
@Target(value = ElementType.METHOD)//注解可以用在哪些地方
@Retention(value = RetentionPolicy.RUNTIME)//表示注解在哪个阶段还有效，一般是用RUNTIME
//RUNTIME>CLASS>SOURSE
@Documented//表示是否生成文档到javadoc中
@Inherited//表示子类可以继承父类的注解
@interface MyAnnotation{//定义一个注解
    
}
```



#### 自定义注解

**当注解中只有一个value属性是(必须是value)，可以省略参数名**

```java
public class AnnotationTest {
    @MyAnnotation("haha")
    public void test(){
    }
}
//定义一个注解
@Target(value = ElementType.METHOD)//注解可以用在哪些地方
@Retention(value = RetentionPolicy.RUNTIME)//表示注解在哪个阶段还有效，一般是用RUNTIME
//RUNTIME>CLASS>SOURSE
@Documented//表示是否生成文档到javadoc中
@Inherited//表示子类可以继承父类的注解
@interface MyAnnotation{//定义一个注解
    String value();
}
```



#### JMM

java内存模型

> JMM变量同步的8个操作

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/JMM%E5%8F%98%E9%87%8F%E4%BB%8E%E4%B8%BB%E5%AD%98%E5%A4%8D%E5%88%B6%E5%88%B0%E5%B7%A5%E4%BD%9C%E5%86%85%E5%AD%98.PNG)

同步规则分析：

1）不允许一个线程无原因地（没有发生过任何assign操作）把数据从工作内存同步会主内存中

2）一个新的变量只能在主内存中诞生，不允许在工作内存中直接使用一个未被初始化（load或者assign）的变量。即就是对一个变量实施use和store操作之前，必须先自行assign和load操作。

3）一个变量在同一时刻只允许一条线程对其进行lock操作，但lock操作可以被同一线程重复执行多次，多次执行lock后，只有执行相同次数的unlock操作，变量才会被解锁。lock和unlock必须成对出现。

4）如果对一个变量执行lock操作，将会清空工作内存中此变量的值，在执行引擎使用这个变量之前需要重新执行load或assign操作初始化变量的值。

5）如果一个变量事先没有被lock操作锁定，则不允许对它执行unlock操作；也不允许去unlock一个被其他线程锁定的变量。

6）对一个变量执行unlock操作之前，必须先把此变量同步到主内存中（执行store和write操作）



## 23种设计模式

### 单例模式

**1.懒汉式**

①一般懒加载

```java
public class TestTemp {
    private static TestTemp testTemp;//先不实例化
    private TestTemp(){
    }
    public static TestTemp getTestTemp(){
        if(testTemp==null){
            testTemp = new TestTemp();//需要的时候再实例化
        }
        return testTemp;
    }
}
```



②直接加重锁

```java
public class TestTemp {
    private static TestTemp testTemp;//先不实例化
    private TestTemp(){
    }
    public static synchronized TestTemp getTestTemp(){
        if(testTemp==null){
            testTemp = new TestTemp();//需要的时候再实例化
        }
        return testTemp;
    }
}
```



③双检锁

```java
public class SingleTest {
    private static volatile SingleTest single;//volatile 防止指令重排
    private String name;

    private SingleTest(){
        //防止反射破坏
        if(single!=null) {
            throw new IllegalArgumentException("单例模式，只允许创建一个单例！");
        }
        System.out.println("实例化");
    }
    public static SingleTest getST(){
        if(single==null){//若已经被实例化则不再创建
            synchronized (SingleTest.class){//加锁占有，但是注意：加锁时候可能已经有线程进入if代码块，正在等待，当释
                							//放锁时就会同时创建多个实例。
                if(single==null){//再次判空，这样除了第一个抢到锁的线程，后面等待的线程就无法再次创建了。
                    single = new SingleTest();
                }
            }
        }
        return single;
    }
    public void sayHello(){
        System.out.println("Hello!");
    }
    private void setName(String name){
        this.name = name;
    }
    public String getName(){
        return this.name;
    }
}
```



**2.饿汉式**

```java
//只有在类加载时候才会初始化。缺点：无论你是否想要，他都会在类加载时候创建一个对象
public class TestTemp {
    private static TestTemp testTemp = new TestTemp();//
    private TestTemp(){
    }
    public static TestTemp getTestTemp(){
        return testTemp;
    }
}
```



**3.枚举函数**

```java
//枚举类，缺点：无法实现懒加载，因为枚举类在程序启动之初就已经把枚举内容完全构建好了
public enum  TestTemp {
    INSTANCE;
    public void say(){
        System.out.println("sb");
    }
}

//实现类
public class Atest {
    public static void main(String[] args) {
        TestTemp testTemp = TestTemp.INSTANCE;
        TestTemp testTemp1 = TestTemp.INSTANCE;
        System.out.println(testTemp.hashCode()+"   "+testTemp1.hashCode());
        testTemp.say();
    }
}

/*结果
1401420256   1401420256
sb
*/
```



**4.静态内部类**

```java
//利用静态内部类不会随着外部类加载而加载，且只在调用时候加载一次特性
public class TestTemp {
    private static class SingleTestTemp{
        private static final TestTemp Instance = new TestTemp();
    }
    private TestTemp(){}
    public static TestTemp getTestTemp(){
        return SingleTestTemp.Instance;
    }
}
```





#### 防反射破坏

①反射破坏

```java
//通过反射获取构造器，再获取私有权限，最后通过构造器直接创建实例
public class DestroySingle {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException{
        Class c1 = Class.forName("com.redis.springboot.SingleTest");
        Constructor <SingleTest>constructor = c1.getDeclaredConstructor();
        constructor.setAccessible(true);
        SingleTest singleTest1 = constructor.newInstance();
        SingleTest singleTest2 = constructor.newInstance();
        System.out.println(singleTest1.hashCode());
        System.out.println(singleTest2.hashCode());
    }
}
```



②防止反射破坏

**确切可行的：**通过枚举类型实现单例模式(反射一定无法破坏枚举，就算反射获取构造函数时候加上了枚举类构造函数对应的String和int参数，仍然无法获取构造器)

**自欺欺人的**：加一个静态标志位，在私有构造方法中判断标志位，通过标志位判断是否已经创建过实例。``但是``要知道反射不仅可以获取构造器，属性也是可以获取的，所以反射破坏时可以修改标志位，从而仍然达到破坏的效果。



#### 反序列化和克隆

```java
public class SingleTest implements Serializable,Cloneable {
    private static volatile SingleTest single;
    private String name;
    private static boolean flag = true;

    private SingleTest(){
        //防止反射破坏(假)
        if(flag) {
            synchronized (SingleTest.class){
                if (flag){
                    flag = false;
                }
            }
        }else{
            throw new IllegalArgumentException("请勿反射破坏！");//实际上仍然可以破坏
        }
    }
    public static SingleTest getST(){
        if(single==null){
            synchronized (SingleTest.class){
                if(single==null){
                    single = new SingleTest();
                }
            }
        }
        return single;
    }

    @Override
    public SingleTest clone() throws CloneNotSupportedException{//防止克隆破坏
        return single;
    }

    private Object readResolve(){//防止序列化破坏
        return single;
    }

    public void sayHello(){
        System.out.println("Hello!");
    }
    private void setName(String name){
        this.name = name;
    }
    public String getName(){
        return this.name;
    }
}

```



### 建造者模式



### 流（字节流、字符流、缓存）

### 线程-进程

进程：程序的一次执行，是资源分配的基本单位。

线程：用于执行具体任务，是cpu调度和分配的基本单位。

### 线程、线程池和并发

> 线程有几种状态？

创建-运行-阻塞-等待-超时等待-终止

> 并发时list可能出现的问题及解决

**并发修改异常**：(注释处是两种解决方法)

```java
public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        //List<String> list = new Vector<>();
        //List<String> list = Collections.synchronizedList(new ArrayList<>());
        //List<String> list = new CopyOnWriteArrayList<>();
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                list.add(UUID.randomUUID().toString());
                System.out.println(list);
            }).start();
        }
    }
```

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E5%B9%B6%E5%8F%91%E4%BF%AE%E6%94%B9%E5%BC%82%E5%B8%B8.PNG)

> Set问题和map一样，set本质就是map的key，所以不能重复



#### JUC三大辅助类

##### CountDownLatch

**用于某些必须要执行的任务**

```java
public class TestCountDownLatch {//减法计数器
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(5);
        for (int i = 0; i < 5; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"走了");
                countDownLatch.countDown();//线程数量减一
            },String.valueOf(i)).start();
        }
        countDownLatch.await();//等待线程数量归零，再向下执行
        System.out.println("下面的没走成！");
    }
}
/*结果
1走了
0走了
2走了
3走了
4走了
下面的没走成！
*/
```

##### CyclicBarrier

**用于将某些任务放在一定数量必须的任务之后**

```java
public class TestCyclicBarrier {//加法计数器
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{//当线程计数器满7再执行中间业务代码
            System.out.println("葫芦娃都牺牲了！");
        });
        for (int i = 0; i < 7; i++) {
            final int temp = i;
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"击杀了第"+temp+"个葫芦娃！");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

##### Semaphore

**控制最大执行线程数**

```java
public class TestSemaphore {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);//最大同时执行线程数
        System.out.println("现在有3辆去幼儿园的车");
        for (int i = 0; i < 8; i++){
            final int id = i//注意定义一个final类型接收外值
            new Thread(()->{
                try {
                    semaphore.acquire();//获取1个信号量，同时将可用信号量数减一
                    System.out.println("老司机"+id+"成功上车！");
                    TimeUnit.SECONDS.sleep(2);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release();//释放1个信号量
                }
            }).start();
        }
    }
}
```



#### JUC4大函数式接口

> Function函数型接口

```java
//接收一个参数，返回一个值
Function <Integer,Integer>function = (str)->{return str;};
        System.out.println(function.apply(2));
/*结果
2
*/
```



> Predicate限制型接口

```java
//接收一个参数，判断参数并返回一个bool值
Predicate predicate = (str)->{return str=="a";};
        System.out.println(predicate.test("a"));
/*结果
true
*/
```



> Supplier生产型接口

```java
//没有参数，返回一个值
Supplier <Integer>supplier = ()->{return 1;};
        System.out.println(supplier.get());
/*结果
1
*/
```



> Consumer消费型接口

```java
//接收一个参数，无返回值
Consumer <Integer>consumer = (str)->{System.out.println(str);};
        consumer.accept(1);
/*结果
1
*/
```



#### BlockingQueue

4组API

|   操作   | 返回bool，抛出异常 | 返回bool，不抛出异常 | 阻塞等待 |        超时等待         |
| :------: | :----------------: | :------------------: | :------: | :---------------------: |
|   添加   |        add         |        offer         |   put    | offer(元素、时间、单位) |
|   移除   |       remove       |         poll         |   take   | poll(元素、时间、单位)  |
| 判断队首 |      element       |         peek         |    -     |            -            |



#### 线程池

##### 三大方法

```
//        ExecutorService executorService = Executors.newSingleThreadExecutor();//单个线程
//        ExecutorService executorService = Executors.newFixedThreadPool(5);//固定5个线程大小
//        ExecutorService executorService = Executors.newCachedThreadPool();//可伸缩，根据需求和硬件性能变化
```

##### 七个参数

```java
int corePoolSize,//核心池大小
int maximumPoolSize,//线程池最大容量
long keepAliveTime,//超过核心线程数量 后闲置线程的存活时间
TimeUnit unit,//时间单位
BlockingQueue<Runnable> workQueue,//阻塞队列
ThreadFactory threadFactory,//创建线程的线程工厂
RejectedExecutionHandler handler//拒绝策略
////////////////////////////////////////////////////////////////////////////////////////////////

```

##### 四种拒绝策略

线程处理过程：任务进来先去核心线程池，当核心线程池满了再进入任务就放到阻塞队列中等待，若阻塞队列也满了，再开启最大线程池容量除核心线程池外的其他线程池，若也满了还有任务进入就执行4大拒绝策略。

```
public class TestExecutorService {
    public static void main(String[] args) {
        int CPUnumber = Runtime.getRuntime().availableProcessors();//获得逻辑CPU核数，即为线程池最大容量(CPU密集型)
        //也可以根据实际情况取最大容量为某程序最大线程数得“两倍”(IO密集型)

        ExecutorService executorService = new ThreadPoolExecutor(
                3,
                CPUnumber,//本机为4
                2,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.CallerRunsPolicy()//当线程池和阻塞队列都满了还有任务进入就将该线程交还给给出															  任务的线程处理，此处是main线程
                //new ThreadPoolExecutor.AbortPolicy()//当线程池和阻塞队列都满了还有任务进入就抛出异常
                //new ThreadPoolExecutor.DiscardOldestPolicy()//当线程池和阻塞队列都满了就让新来的任务去和阻塞队列																 中等待最久的竞争，不抛出异常
                //new ThreadPoolExecutor.DiscardPolicy()//当线程池和阻塞队列都满了就放弃后面的任务
        );
        try {
            for (int i = 0; i < 100; i++) {
                executorService.execute(()->{
                    System.out.println(Thread.currentThread().getName());
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            executorService.shutdown();//关闭线程池
        }
    }
}
```

#### Map

#### hashmap

1.7与1.8区别：

1. 1.7扩容时候采用头插法，在多线程情况下容易出现循环链表从而导致死循环；1.8当链表长度到达设置阈值（默认为8）后转换为红黑树，采用尾插法从而避免了循环链表的问题。
2. 1.8多加了一个参数用于判断链表长度，如果长度达到一定值会将链表转换为红黑树。同时将HashEntry更名为Node

##### jdk1.7中：

###### 底层：

数组+链表

###### 结构图

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/jdk1.7hashmap%E7%BB%93%E6%9E%84%E5%9B%BE.PNG)

###### 基础数值：

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/jdk1.7hashmap%E5%BA%95%E5%B1%82.PNG)

1. 初始化桶大小`16`，因为底层是数组，所以这是数组默认的大小。
2. 桶最大值。
3. 默认的负载因子（`0.75`）
4. table真正存放数据的数组。
5. Map存放数量的大小。
6. 桶大小，可在初始化时显式指定。
7. 负载因子，可在初始化时显式指定。

###### put方法：

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/jdk1.7hashmap_put.PNG)

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/jdk1.7hashmap_put2.PNG)

- 判断当前数组是否需要初始化。
- 如果 key 为空，则 put 一个空值进去。
- 根据 key 计算出 hashcode。
- 根据计算出的 hashcode 定位出所在桶。
- 如果桶是一个链表则需要遍历判断里面的 hashcode、key 是否和传入 key 相等，如果相等则进行覆盖，并返回原来的值。
- 如果桶是空的，说明当前位置没有数据存入；新增一个 Entry 对象写入当前位置。
- 当调用 addEntry 写入 Entry 时需要判断是否需要扩容。
- 如果需要就进行``两倍扩充``，并将当前的 key 重新 hash 并定位。
- 而在 createEntry中会将当前位置的桶传入到新建的桶中，如果当前桶有值就会在位置形成链表。

###### get方法

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/jdk1.7hashmap_get.PNG)

- 首先也是根据 key 计算出 hashcode，然后定位到具体的桶中。
- 判断该位置是否为链表。
- 不是链表就根据 key、key 的 hashcode是否相等来返回值。
- 为链表则需要遍历直到 key 及 hashcode 相等时候就返回值。
- 啥都没取到就直接返回 null 。

##### jdk1.8中：

###### 底层：

数组+链表+红黑树

###### 结构图：

![image-20201119192821492](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20201119192821492.png)

###### 基础数值：

与jdk1.7不同之处：

- 新增`TREEIFY_THRESHOLD`用于判断是否需要将链表转换为红黑树的阈值。
- HashEntry 修改为 Node。

###### put方法

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/jdk1.8hashmap_put.PNG)

1. 判断当前桶是否为空，空的就需要初始化（resize 中会判断是否进行初始化）。
2. 根据当前 key 的 hashcode 定位到具体的桶中并判断是否为空，为空表明没有 Hash 冲突就直接在当前位置创建一个新桶即可。
3. 如果当前桶有值（ Hash 冲突），那么就要比较当前桶中的 `key、key 的 hashcode` 与写入的 key 是否相等，相等就赋值给 `e`,在第 8 步的时候会统一进行赋值及返回。
4. 如果当前桶为红黑树，那就要按照红黑树的方式写入数据。
5. 如果是个链表，就需要将当前的 key、value 封装成一个新节点写入到当前桶的后面（形成链表）。
6. 接着判断当前链表的大小是否大于预设的阈值，大于时就要转换为红黑树。
7. 如果在遍历过程中找到 key 相同时直接退出遍历。
8. 如果 `e != null` 就相当于存在相同的 key,那就需要将值覆盖。
9. 最后判断是否需要进行扩容。

###### get方法

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/jdk1.8hashmap_get.PNG)

- 首先将 key hash 之后取得所定位的桶。
- 如果桶为空则直接返回 null 。
- 否则判断桶的第一个位置(有可能是链表、红黑树)的 key 是否为查询的 key，是就直接返回 value。
- 如果第一个不匹配，则判断它的下一个是红黑树还是链表。
- 红黑树就按照树的查找方式返回值。
- 不然就按照链表的方式遍历匹配返回值。



#### ConcurrentHashMap

1.7与1.8区别：

1. 1.8将1.7中的分段锁改为CAS+Synchronized

2. 1.8底层增加了红黑树，

##### jdk1.7中：

###### 底层

数组+链表

###### 结构图

采用分段锁，效率比同期的重锁synchronized高很多。每次锁一个segment，其中 Segment 继承于 ReentrantLock。不会像 HashTable 那样不管是 put 还是 get 操作都需要做同步处理，理论上 ConcurrentHashMap 支持 CurrencyLevel (Segment 数组数量)的线程并发。每当一个线程占用锁访问一个 Segment 时，不会影响到其他的 Segment。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/jdk1.7concurrenthashmap%E7%BB%93%E6%9E%84%E5%9B%BE.PNG)

###### 基础数值

![image-20201119203419289](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20201119203419289.png)

其中value和hashentry都是volatile修饰的，可以保证可见性。

###### put

首先是通过 key 定位到 Segment，之后在对应的 Segment 中进行具体的 put。

首先第一步的时候会尝试获取锁，如果获取失败肯定就有其他线程存在竞争，则利用 `scanAndLockForPut()` 自旋获取锁。

利用trylock尝试自旋获取锁。

如果重试的次数达到了 `MAX_SCAN_RETRIES` 则改为阻塞锁获取，保证能获取成功。

然后正常判空、非空则覆盖，空则加入。

最后释放前面Segment获取的锁

###### get

hash定位、再取值(整个过程不需要加锁，很高效)

##### jdk1.8中：

###### 底层

数组+链表+红黑树

###### 结构图

将1.7中分段锁改为CAS+Synchronized，hashEntry改为Node(作用一样)

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/jdk1.8hashmap%E7%BB%93%E6%9E%84%E5%9B%BE.PNG)

###### put

- 根据 key 计算出 hashcode 。
- 判断是否需要进行初始化。
- `f` 即为当前 key 定位出的 Node，如果为空表示当前位置可以写入数据，利用 CAS 尝试写入，失败则自旋保证成功。
- 如果当前位置的 `hashcode == MOVED == -1`,则需要进行扩容。
- 如果都不满足，则利用 synchronized 锁写入数据。
- 如果数量大于 `TREEIFY_THRESHOLD` 则要转换为红黑树。

###### get

- 根据计算出来的 hashcode 寻址，如果就在桶上那么直接返回值。
- 如果是红黑树那就按照树的方式获取值。
- 就不满足那就按照链表的方式遍历获取值。



### CAS

> 什么是CAS

顾名思义，比较并交换，即：如果给出的值是预期的值，那么就更新，否则就不更新。

```java
public class TestCAS {
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(2020);//原子类
        //如果我期望的值达到了，就更新
        System.out.println(atomicInteger.compareAndSet(2019, 2021));
        System.out.println(atomicInteger.get());
        System.out.println(atomicInteger.compareAndSet(2020, 2021));
        System.out.println(atomicInteger.get());
    }
}
/*结果
false
2020
true
2021
*/
```

> Unsafe类

由于java无法直接操作内存，所以留了一个类用于调用c++的方法操作内存，就是unsafe类。

![image-20201122100035542](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20201122100035542.png)

其中有一个值+1的内存操作：取出内存地址偏移值和期望值v比较，如果相等就让值+1（与CAS原理相同）

![image-20201122095758009](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20201122095758009.png)

> ABA问题

箱子里放着一个蛋糕，B把蛋糕吃了，然后又往箱子里放了一个一样的蛋糕，这时候A再去拿这个蛋糕，虽然看着还是一样的蛋糕，但是实际上已经不是以前那个蛋糕了，可是A不知道，以为还是之前那个蛋糕。

**解决**：用乐观锁思想，利用原子引用AtomicStampedReference加一个版本号，每次修改“蛋糕”就让版本号+1，每次除了比较值外还要判断版本号是否相等，再进行变换。

```java
public class TestCAS {
    public static void main(String[] args){
        AtomicStampedReference<Integer> atomicStampedReference = new AtomicStampedReference(1,1);
        //捣乱的B线程
        new Thread(()->{
            System.out.println(atomicStampedReference.compareAndSet(1, 2, atomicStampedReference.getStamp(), atomicStampedReference.getStamp() + 1));
            System.out.println("A=>"+atomicStampedReference.getReference());
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(atomicStampedReference.compareAndSet(2, 1, atomicStampedReference.getStamp(), atomicStampedReference.getStamp() + 1));
            System.out.println("A=>"+atomicStampedReference.getReference());
        },"B").start();

        //A线程
        new Thread(()->{
            int stamp = atomicStampedReference.getStamp();
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("B=>"+atomicStampedReference.getReference());
            System.out.println(atomicStampedReference.compareAndSet(1, 10, stamp, atomicStampedReference.getStamp() + 1));
        },"A").start();
    }
}
/*结果
true
A=>2
true
A=>1
B=>1//这里虽然B的值是1，但是版本号不和之前一样，故而修改失败！
false
*/
```



**注意一个大坑**：如果比较的对象值是Integer，那么值的范围只能在-128~127之间，因为这之间的值都是在cache中产生，可以直接用==比较，而超出这个区间的值是在堆中产生的，用\==比较得出结果一定是不相等！因为引用地址必然产生了变化！推荐使用equals比较。



## 锁机制

> Lock，锁住的是加锁与解锁之间的代码块

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/Lock.PNG)

> Synchronized和Lock的区别

1. Synchronized是java内置关键字，lock是一个接口

2. Synchronized无法操作中间过程，Lock可以判断是否获取锁

3. Synchronized会自动释放锁，Lock需要手动释放

4. Synchronized遇到前面线程阻塞会一直等待，Lock可以trylock尝试获取锁

5. Synchronized是可重入、不可中断、非公平的，Lock是可重入、可以判断中断条件、可以选择公平与否

6. **一般情况Synchronized够用了，当它灵活性不足时再选择Lock**

   

### 常见锁问题

#### 生产者消费者问题(虚假唤醒)

###### Synchronized实现

```java
//当只有一个生产者和一个消费者时候，通过if判断等待条件不会有问题
public class Atest {
    public static void main(String[] args) {
        PC pc = new PC();
        new Thread(()->{ for (int i = 0; i < 10; i++) pc.increment(); },"A").start();//生产
        new Thread(()->{ for (int i = 0; i < 10; i++) pc.decrement(); },"B").start();//消费
    }
}
class PC{
    public int number = 0;
    //生产
    public synchronized void increment(){
        if(number!=0){
            //等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        number++;
        System.out.println(Thread.currentThread().getName()+"->"+number);
        this.notifyAll();
    }

    //消费
    public synchronized void decrement(){
        if(number==0){
            //等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        number--;
        System.out.println(Thread.currentThread().getName()+"->"+number);
        this.notifyAll();
    }
}

/*结果
B->0
A->1 重复10次
*/

//但是当多个生产者和消费者时，if判断可能出现虚假唤醒现象，比如：A、C生产，B、D消费，当number!=0时A、C同时等待，此时B消费了，number=0，A、C同时收到唤醒消息，由于if只判断一次，不需要再判断，所以二者都执行了生产操作，于是导致wait失效。换句话说没有唤醒想要唤醒的单个线程，即所谓的虚假唤醒。解决办法：将if判断改为while判断
public class Atest {
    public static void main(String[] args) {
        PC pc = new PC();
        new Thread(()->{ for (int i = 0; i < 10; i++) pc.increment(); },"A").start();//生产
        new Thread(()->{ for (int i = 0; i < 10; i++) pc.decrement(); },"B").start();//消费
        new Thread(()->{ for (int i = 0; i < 10; i++) pc.increment(); },"C").start();//生产
        new Thread(()->{ for (int i = 0; i < 10; i++) pc.decrement(); },"D").start();//消费
    }
}
class PC{
    public int number = 0;
    //生产
    public synchronized void increment(){
        while(number!=0){
            //等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        number++;
        System.out.println(Thread.currentThread().getName()+"->"+number);
        this.notifyAll();
    }

    //消费
    public synchronized void decrement(){
        while(number==0){
            //等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        number--;
        System.out.println(Thread.currentThread().getName()+"->"+number);
        this.notifyAll();
    }
}
```

###### Lock实现(condition精确唤醒)

```java
public class TestLock {
    public static void main(String[] args) {
        Ticket1 ticket = new Ticket1();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                ticket.increment();
            }
        },"线程A").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                ticket.decrement();
            }
        },"线程B").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                ticket.decrement1();
            }
        },"线程C").start();
    }
}

class Ticket1{
    private int number = 0;//0A,1B,2C
    Lock lock = new ReentrantLock();
    Condition condition0 = lock.newCondition();
    Condition condition1 = lock.newCondition();
    Condition condition2 = lock.newCondition();
    //生产
    public void increment(){
        lock.lock();
        try {
            while(number!=0){
                condition0.await();
            }
            System.out.println(Thread.currentThread().getName());
            number=1;//控制A后执行B
            condition1.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    //消费
    public void decrement(){
        lock.lock();
        try {
            while(number!=1){
                condition1.await();
            }
            System.out.println(Thread.currentThread().getName());
            number = 2;
            condition2.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void decrement1(){
        try {
            lock.lock();
            while(number!=2){
                condition2.await();
            }
            System.out.println(Thread.currentThread().getName());
            number = 0;
            condition0.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}

/*结果
线程A
线程B
线程C 重复10次
*/
```

#### 8锁问题

> ①synchronized锁的对象是方法的调用者 ②一个对象只有一把锁，先拿到对象锁的先执行

```java
public class lock8{
    public static void main(String[] args) {

        Phone phone = new Phone();

        new Thread(()->{
            phone.sendMail();
        },"A").start();

        //睡眠一秒
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(()->{
            phone.call();
        },"B").start();
    }
}
class Phone{
    public synchronized void sendMail(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("发送邮件");
    }
    public synchronized void call(){
        System.out.println("打电话");
    }
}
/*结果
发送邮件
打电话
*/

//如果中间不睡眠1s
/*结果
先发邮件或者先打电话都有可能，看谁先抢到phone的对象锁
*/
```

> ③若一个方法有锁，一个方法没锁，则先后取决于执行顺序（与没锁一样，考虑睡眠时间）

```java
public class lock8{
    public static void main(String[] args) {

        Phone phone = new Phone();

//        new Thread(()->{
//            phone.sendMail();
//        },"A").start();

        new Thread(()->{
            phone.call();
        },"B").start();

        new Thread(()->{
            phone.sayhello();
        },"C").start();
    }
}
class Phone{
    public synchronized void sendMail(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("发送邮件");
    }
    public synchronized void call(){
        System.out.println("打电话");
    }
    public void sayhello(){
        System.out.println("hello");
    }
}
/*结果
发邮件和hello都可能先
*/
```

> ④如果锁的对象不同，则先后取决于执行顺序(算上睡眠时间)

```java
public class lock8{
    public static void main(String[] args) {

        Phone phone1 = new Phone();
		Phone phone2 = new Phone();

        new Thread(()->{
            phone1.sendMail();
        },"A").start();

        //睡眠一秒
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(()->{
            phone2.call();
        },"B").start();
    }
}
class Phone{
    public synchronized void sendMail(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("发送邮件");
    }
	public synchronized void call(){
        System.out.println("打电话");
    }
}
/*结果
打电话
发送短信
*/
```



> ⑤⑥如果锁的方法是静态方法，则锁的是当前类的class对象，无论实例化多少个对象，锁的都是同一个！

```java
public class lock8{
    public static void main(String[] args) {

        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        new Thread(()->{
            phone1.sendMail();
        },"A").start();

        new Thread(()->{
            phone2.call();
        },"B").start();
    }
}
class Phone{
    public static synchronized void sendMail(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("发送邮件");
    }
    public static synchronized void call(){
        System.out.println("打电话");
    }
}
/*结果
发送邮件
打电话
*/
```

> ⑦⑧如果一个加static一个不加，则锁的必然不是同一个对象，先后顺序取决于执行顺序(考虑睡眠时间)

```java
public class lock8{
    public static void main(String[] args) {

        Phone phone1 = new Phone();
        Phone phone2 = new Phone();

        new Thread(()->{
            phone1.sendMail();
        },"A").start();

        new Thread(()->{
            phone2.call();
        },"B").start();
    }
}
class Phone{
    public static synchronized void sendMail(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("发送邮件");
    }
    public synchronized void call(){
        System.out.println("打电话");
    }
}
/*结果
打电话
发送邮件
*/
```



### 可重入锁

```java
Lock lock = new ReentrantLock();

//加锁-解锁过程
public void increment(){
        lock.lock();//try外加锁
        try {//try内业务
            while(number!=0){
                condition0.await();
            }
            System.out.println(Thread.currentThread().getName());
            number=1;//控制A后执行B
            condition1.signal();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            lock.unlock();//finally中解锁
        }
    }
```

可重入表示可以嵌套加锁，然后逐层解锁。当拿到外层锁时会自动获取内层锁。



### 公平锁

不可插队，先来后到(下面图，ReentrantLock参数为true)

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E5%85%AC%E5%B9%B3%E9%94%81%E5%92%8C%E9%9D%9E%E5%85%AC%E5%B9%B3%E9%94%81.PNG)

### 非公平锁

可以插队，是``默认``锁机制(上面图，ReentrantLock参数为false或者不加参数)



### 乐观锁

操作数据时加version版本号，提交更新操作时判断version值是否被更改，如果已经被更改则更新失败。

### 悲观锁

操作数据时候对要操作的数据加锁。

> 乐观锁与悲观锁的适用场景

如果需要高响应速度，则采用乐观锁。

如果冲突频率高或者版本号冲突后重试的代价很大，则采用悲观锁。

**数据库默认是悲观锁。**



### 自旋锁

```java
//自写自旋锁
public class TestSpinLock{
    AtomicReference atomicReference = new AtomicReference();

    //自旋锁加锁
    public void myLock(){
        Thread thread = Thread.currentThread();
        System.out.println(thread.getName()+"->"+"myLock");
            while(!atomicReference.compareAndSet(null,thread)){
                //如果锁已被获取则自旋等待，如果解锁后期望值为空则可以获取锁，并将期望着新设为当前线程
            }
    }

    //自旋锁解锁
    public void myUnLock(){
        Thread thread = Thread.currentThread();
        System.out.println(thread+"->"+"myUnLock");
        atomicReference.compareAndSet(thread,null);//解锁，期望值是锁住的线程则将期望值重新设为null
    }
}
```

```java
//自旋锁测试
public class Atest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        TestSpinLock lock = new TestSpinLock();
        new Thread(()->{
            //TestSpinLock lock = new TestSpinLock();
            lock.myLock();
            try {
                TimeUnit.SECONDS.sleep(5);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                lock.myUnLock();
            }
        },"A").start();

        new Thread(()->{
            //TestSpinLock lock = new TestSpinLock();
            lock.myLock();
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                lock.myUnLock();
            }
        },"B").start();
    }
}
/*结果
A->myLock
B->myLock
Thread[A,5,main]->myUnLock//虽然A睡眠时间长，但是由于加的是同一把锁，故而A先获取锁，在A释放锁前B只能一直自旋等待
Thread[B,5,main]->myUnLock
*/
```



### 读写锁

> 读锁/写锁的作用

读锁：可读不可写

写锁：读写都不可以

> 代码样例

```java
public class TestReadWriteLock {
    public static void main(String[] args) {
        MyReadWriteLock myReadWriteLock = new MyReadWriteLock();
        for (int i = 0; i < 5; i++) {//5个写线程
            final int id = i;
            new Thread(()->{
                myReadWriteLock.write(Thread.currentThread().getName(),id);
            },String.valueOf(i)).start();
        }
        for (int i = 0; i < 5; i++) {//5个读线程
            final int id = i;
            new Thread(()->{
                myReadWriteLock.read(Thread.currentThread().getName(),id);
            },String.valueOf(i)).start();
        }
    }
}
class MyReadWriteLock {
    Map <String,Integer>map = new HashMap<>();
    ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    public void read(String key,int val){
        readWriteLock.readLock().lock();//加读锁，读时不能写，保证数据一致
        try {
            System.out.println("开始读取"+Thread.currentThread().getName());
            map.put(key,val);
            System.out.println("读取"+Thread.currentThread().getName()+"完成");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }
    }
    public void write(String key,int val){
        readWriteLock.writeLock().lock();//加写锁，写时不能读写
        try {
            System.out.println("开始写"+Thread.currentThread().getName());
            map.put(key,val);
            System.out.println("写入"+Thread.currentThread().getName()+"完成");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.writeLock().unlock();
        }
    }
}
/*结果
开始写0
写入0完成
开始写1
写入1完成
开始写2
写入2完成
开始写3
写入3完成
开始写4
写入4完成//很明显，写入都是不可插队的

开始读取0
读取0完成
开始读取3//而读取是可以被读操作插队的
开始读取4//
读取4完成
读取3完成
开始读取1
读取1完成
开始读取2
读取2完成
*/
```





### 死锁

> 什么是死锁

**死锁概念及产生原理**

   **概念：** 多个并发进程因争夺系统资源而产生相互等待的现象。

   **原理：** 当一组进程中的每个进程都在等待某个事件发生，而只有这组进程中的其他进程才能触发该事件，这就称这组进程发生了死锁。通俗点说，互相等待，不愿放手。

   **本质原因：**

​     1）、系统资源有限。

​     2）、进程推进顺序不合理。

> 死锁产生的4个必要条件

  **1、互斥：** 某种资源一次只允许一个进程访问，即该资源一旦分配给某个进程，其他进程就不能再访问，直到该进程访问结束。

  **2、占有且等待：** 一个进程本身占有资源（一种或多种），同时还有资源未得到满足，正在等待其他进程释放该资源。

  **3、不可抢占：** 别人已经占有了某项资源，你不能因为自己也需要该资源，就去把别人的资源抢过来。

  **4、循环等待：** 存在一个进程链，使得每个进程都占有下一个进程所需的至少一种资源。

   任意破坏一个就能解除死锁。

> 避免死锁——银行家算法（了解）

见博客：[https://blog.csdn.net/qq_36260974/article/details/84404369](https://blog.csdn.net/qq_36260974/article/details/84404369)

> 死锁样例

```java
public class TestDeadLock {
    public static void main(String[] args) {
        new Thread(new MyDeadThread("A","B")).start();
        new Thread(new MyDeadThread("B","A")).start();
    }
}
@AllArgsConstructor
class MyDeadThread implements Runnable{
    private String first="",second="";
    @Override
    public void run() {
        synchronized (first){
            System.out.println(Thread.currentThread().getName()+" get "+first+"->want to get "+second);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (second){
                System.out.println(Thread.currentThread().getName()+" get "+second);
            }
        }
    }
}
/*
Thread-0 get A->want to get B
Thread-1 get B->want to get A
......//互相等待到天荒地老。。。。。。。。。。。。。。。。。。。。。。
*/
```

> 死锁排查

1、使用`jps -l`定位进程号

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E6%AD%BB%E9%94%81%E6%8E%92%E6%9F%A51.PNG)

2、使用`jstack-进程号`查看堆栈信息

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E6%AD%BB%E9%94%81%E6%8E%92%E6%9F%A52.png)

3、根据堆栈信息显示的死锁点和原因排查。

当然也可以根据日志排查死锁，但是不推荐。



# javaweb套餐

### json

轻量级数据交换格式，本质也是一种字符串。

### ajax

一种数据交换格式，实现页面的异步不重载更新。

## ==springboot（mybatis）==

#### IDEA热部署配置

1.导入maven依赖

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <version>2.3.3.RELEASE</version>
</dependency>
    
//若配置完还不成功，加上下面的依赖
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
    <fork>true</fork><!--必须添加这个配置-->
    </configuration>
</plugin>
```

2.打开IDEA自动编译(静态)

具体步骤：打开顶部工具栏  File -> Settings -> Default Settings -> Build -> Compiler  然后勾选 Build project automatically 。

3.打开IDEA自动编译(动态)

具体步骤：同时按住 Ctrl + Shift + Alt + / 然后进入Registry ，勾选自动编译并调整延时参数。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E5%BC%80%E5%90%AFIDEA%E5%8A%A8%E6%80%81%E8%87%AA%E7%BC%96%E8%AF%91.PNG)



![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E5%BC%80%E5%90%AFIDEA%E6%9B%B4%E6%96%B0.png)

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E5%BC%80%E5%90%AFIDEA%E8%87%AA%E5%8A%A8%E6%9B%B4%E6%96%B0.png)



#### mybatis执行流程

###### 完全版：

![mybatis执行流程](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/mybatis%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B.png)

###### 精简版：

![image-20201002000037702](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20201002000037702.png)



#### springboot执行流程

###### 核心注解：

**@EnableAutoConfiguration**：从classpath中搜寻所有的META-INF/spring.factories配置文件，并将其中org.springframework.boot.autoconfigure.**EnableutoConfiguration**对应的配置项通过**反射**实例化为对应的标注了@Configuration的JavaConfig形式的IoC容器配置类，然后汇总为一个并加载到IoC容器。`汇总完最后加载到ApplicationContext`

###### 大致执行流程：

![springboot执行流程](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/springboot%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B.jpg)



### spring IOC AOP

###### bean<->IOC

![image-20201002110253064](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20201002110253064.png)

IoC(控制反转)就是一个对象定义其依赖关系而不创建它们的过程，控制反转通过依赖注入（DI）方式实现对象之间的松耦合关系。

在 Spring 中，**类的实例化、依赖的实例化、依赖的传入**都交由 Spring Bean 容器控制(私有属性、构造方法)

而不是用new方式实例化对象、通过非构造函数方法传入依赖等常规方式。

实质的控制权已经交由程序管理，而不是程序员管理，所以叫做控制反转。

###### AOP

面向切面编程，将程序所需的公共方法横向截取出来，需要的时候直接注入到切点(逻辑业务需要调用的地方)，这样一来，程序员只需要专于逻辑。



###### @Bean注解简介

Spring的@Bean注解用于告诉方法，产生一个Bean对象，然后这个Bean对象交给Spring管理。产生这个Bean对象的方法Spring只会调用一次，随后这个Spring将会将这个Bean对象放在自己的IOC容器中。

SpringIOC 容器管理一个或者多个bean，这些bean都需要在@Configuration注解下进行创建，在一个方法上使用@Bean注解就表明这个方法需要交给Spring进行管理。



### #{}和${}区别：

（#{}会做预处理，而${}是直接拼接字符串）

![image-20200929191526502](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200929191526502.png)

### 后端日期问题：

```java
//日期转字符串（年月日格式）
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");//加时间就yyyy-MM-dd hh:mm:ss
String date = sdf.format(list.get(i).getSaletime());

//日期模糊匹配
DATE_FORMAT(intime, '%Y-%m-%d') LIKE CONCAT('%',#{intime},'%')
    
//后端传往前端显示yyyy-MM-dd格式
@JsonFormat(pattern = "yyyy-MM-dd", timezone = "GMT+8")
private java.util.Date intime;

spring://配置文件中
  jackson:
    date-format: yyyy-MM-dd

//中国标准时间的String-date转换
public final static String FORMAT_STRING2 = "EEE MMM dd yyyy HH:mm:ss z";
SimpleDateFormat sf1 = new SimpleDateFormat(FORMAT_STRING2, Locale.ENGLISH);
Date date = sf1.parse("时间字符串");
```

### 六位随机数生成

```java
public class randomCode {
    public static String randomcode() {
        StringBuilder str = new StringBuilder();
        Random random = new Random();
        for (int i = 0; i < 6; i++) {
            str.append(random.nextInt(10));
        }
        return str.toString();
    }
}
```

### 邮件发送

```java
public class MailUtil {
    private static String myEmailAccount="发件邮箱";//发件人
    private static String myEmailPassword="hjwhrxfcunsggfda";//保护码
    private static String myEmailSMTPHost="smtp.qq.com";
    private static String receiveMailAccount="";//收件人
    private static String smtpPort="465";

    private static String randomStr ;


    public static String sendMail(String email,String username){
        try{
            Properties props = new Properties();                    // 参数配置
            props.setProperty("mail.transport.protocol", "smtp");   // 使用的协议（JavaMail规范要求）
            props.setProperty("mail.smtp.host", myEmailSMTPHost);   // 发件人的邮箱的 SMTP 服务器地址
            props.setProperty("mail.smtp.auth", "true");
            props.setProperty("mail.smtp.port", smtpPort);
            props.setProperty("mail.smtp.socketFactory.class", "javax.net.ssl.SSLSocketFactory");
            props.setProperty("mail.smtp.socketFactory.fallback", "false");
            props.setProperty("mail.smtp.socketFactory.port", smtpPort);

            Session session=Session.getDefaultInstance(props);
            session.setDebug(true);

            receiveMailAccount = email;//更改邮件接收人为注册用户
            MimeMessage message= createMimeMessage(session,myEmailAccount,receiveMailAccount,username);

            Transport transport=session.getTransport();

            transport.connect(myEmailAccount,myEmailPassword);

            transport.sendMessage(message,message.getAllRecipients());

            transport.close();

        }catch (Exception ex){
            System.out.println(ex.getMessage());
        }
        return randomStr;
    }

    private static MimeMessage createMimeMessage(Session session,String sendMail,String receiveMail,String recivename){
        //1. 创建邮件
        MimeMessage message = new MimeMessage(session);
        try{
            //1. 创建邮件
//            MimeMessage message = new MimeMessage(session);

            //2.绑定发件人
            message.setFrom(new InternetAddress(sendMail,"巴蜀超市","UTF-8"));

            //3.绑定收件人
            message.setRecipient(MimeMessage.RecipientType.TO,new InternetAddress(receiveMail,recivename,"UTF-8"));


            //4.邮件主题
            message.setSubject("您此次注册的验证码：","UTF-8");

            //5. 邮件正文
            randomStr = new randomCode().randomcode();
            message.setContent(randomStr,"text/html;charset=UTF-8");

            //6.发送时间
            message.setSentDate(new Date());

            //7.保存设置
            message.saveChanges();

        }catch (Exception ex){
            System.out.println(ex.getMessage());
        }
        return message;
    }
}

```



### 后端解决跨域问题

**①配置Bean函数**

```java
import org.springframework.boot.SpringBootConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * 解决跨域问题
 */
@SpringBootConfiguration
public class CorsConfiguration {
    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**")
                        .allowCredentials(false)
                        .allowedMethods("POST", "GET", "PUT", "OPTIONS", "DELETE")
                        .allowedOrigins("*");
            }
        };
    }

}
```

**②在Controller类上添加一个“@CrossOrigin“注解**

### 后端接收前端传回复杂数组数据

**1.首先，前端一定传回json字符串**

2.**然后后端用map接收，不断通过get(key)方式取出自己需要的值，用相应的存储格式存储！**



### mybatis框架下如何解决sql注入问题。

（1）用#{param}替换所有的${param}

因为${}是拼接sql字符实现没有预编译的查询，因为是无法防御sql注入，而#{}则需要进行预编译，可以很大程度上防止sql注入。

在一些#{}使用时候会报错的地方，如like 查询、in 查询、order by排序等，

a) like: 

select * from Users where username like '%${username}%'

替换成:

==select * from Users where username like concat('%', #{username}, '%')==



b) in

select * from Users where id in (${id})

替换成mybatis框架自带的foreach循环：

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/mybatis_foreach.png)

foreach元素的属性主要有 item，index，collection，open，separator，close。

  1  item表示集合中每一个元素进行迭代时的别名，
  2  index指 定一个名字，用于表示在迭代过程中，每次迭代到的位置，
  3  open表示该语句以什么开始，                          例 ： open="（"  就是以左括号开始
  4  separator 表示在每次进行迭代之间以什么符号作为分隔符，      例 ： separator=","  以逗号分隔
  5  close 表示以什么结束。                              例 ： close=")"   就是以右括号结尾



c) order by

不要直接使用：拼接排序

如：select *from Users order by ${id}

而是在java层面做映射，然后用<if>来做判断

![image-20200929200029197](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200929200029197.png)

### mybatis调用接口：

> mybatis操作数据库流程图示

![mybatis_sqlsession创建](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/mybatis_sqlsession%E5%88%9B%E5%BB%BA.PNG)

> mybatis示例代码

```java
//工具类获取sqlsession，获取sqlsession最好用单例模式
public class MybatisUtil {

    private static SqlSessionFactory sqlSessionFactory;

    //SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得
    static {
        try{
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);//读取文件配置
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);//创建会话工厂
        }catch (IOException e) {
            e.printStackTrace();
        }
    }

    //从 SqlSessionFactory 中获取 SqlSession
    public static SqlSession sqlSession(){
        return sqlSessionFactory.openSession();//获取会话
    }
}
```

```java
//sqlsession用完即关闭
@Test
    public void getAllStudentTeacher(){

        SqlSession sqlSession = MybatisUtil.sqlSession();
        try{
            //获取接口对象
            StudentTeacherDao studentTeacherDao = sqlSession.getMapper(StudentTeacherDao.class);
            List<Teacher> list = new ArrayList<>();
            list = studentTeacherDao.getAllStudentTeacher();
            for (Teacher listST:list) {
                System.out.println(listST.getStudent().getName());
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            sqlSession.close();//及时关闭
        }
    }
```





### resultMap映射配置

![student_po类](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/student_po%E7%B1%BB.PNG)![student数据库](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/student%E6%95%B0%E6%8D%AE%E5%BA%93.PNG)

![student查询所有map属性别名配置](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/student%E6%9F%A5%E8%AF%A2%E6%89%80%E6%9C%89map%E5%B1%9E%E6%80%A7%E5%88%AB%E5%90%8D%E9%85%8D%E7%BD%AE.PNG)

### 日志输出

###### 标准日志

mybatis自带，不需要导包

**配置**：

![stdout_logging](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/stdout_logging.PNG)

**结果**：

![stdout_logging结果](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/stdout_logging%E7%BB%93%E6%9E%9C.PNG)

###### log4j

需要导入log4j包，配置xml文件(或者properties文件)自定义所需的日志信息

```java
# priority  :debug<info<warn<error
#you cannot specify every priority with different file for log4j
log4j.rootLogger=debug,stdout,info,debug,warn,error 

#console
log4j.appender.stdout=org.apache.log4j.ConsoleAppender 
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout 
log4j.appender.stdout.layout.ConversionPattern= [%d{yyyy-MM-dd HH:mm:ss a}]:%p %l%m%n
#info log
log4j.logger.info=info
log4j.appender.info=org.apache.log4j.DailyRollingFileAppender 
log4j.appender.info.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.info.File=./src/log/info.log
log4j.appender.info.Append=true
log4j.appender.info.Threshold=INFO
log4j.appender.info.layout=org.apache.log4j.PatternLayout 
log4j.appender.info.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
#debug log
log4j.logger.debug=debug
log4j.appender.debug=org.apache.log4j.DailyRollingFileAppender 
log4j.appender.debug.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.debug.File=./src/log/debug.log
log4j.appender.debug.Append=true
log4j.appender.debug.Threshold=DEBUG
log4j.appender.debug.layout=org.apache.log4j.PatternLayout 
log4j.appender.debug.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
#warn log
log4j.logger.warn=warn
log4j.appender.warn=org.apache.log4j.DailyRollingFileAppender 
log4j.appender.warn.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.warn.File=./src/log/warn.log
log4j.appender.warn.Append=true
log4j.appender.warn.Threshold=WARN
log4j.appender.warn.layout=org.apache.log4j.PatternLayout 
log4j.appender.warn.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
#error
log4j.logger.error=error
log4j.appender.error = org.apache.log4j.DailyRollingFileAppender
log4j.appender.error.DatePattern='_'yyyy-MM-dd'.log'
log4j.appender.error.File = ./src/log/error.log 
log4j.appender.error.Append = true
log4j.appender.error.Threshold = ERROR 
log4j.appender.error.layout = org.apache.log4j.PatternLayout
log4j.appender.error.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
```

![log4j设置](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/log4j%E8%AE%BE%E7%BD%AE.png)



#### 分页

###### limit

通过map传入开始索引和分页大小，利用SQL limit分页

![image-20201001221819663](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20201001221819663.png)

![image-20201001221932183](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20201001221932183.png)



#### 多表修改

```xml
update product p
join productcategory c
on
     p.categoryId=c.id
join productstock s
on
     p.ProId=s.ProId
<set>
<if test="proName!= null ">
       p.ProName=#{proName}
</if>
<if test="keywords!= null ">
    ,p.Keywords=#{keywords}
</if>
</set>
where
p.ProId=#{proId}
```





#### 多表查询

##### 多对一

###### 子查询

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E5%AD%90%E6%9F%A5%E8%AF%A2.PNG)

一般resultMap中的result表示单个属性，复杂属性则用association(对象)和collection(集合)。复杂属性中可以嵌套select查询。

###### 联表查询

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E8%81%94%E8%A1%A8%E6%9F%A5%E8%AF%A2.PNG)



##### 一对多

###### 子查询

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E4%B8%80%E5%AF%B9%E5%A4%9A%E5%AD%90%E6%9F%A5%E8%AF%A2.PNG)

###### 联表查询

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E4%B8%80%E5%AF%B9%E5%A4%9A%E8%81%94%E8%A1%A8%E6%9F%A5%E8%AF%A2.PNG)



#### SQL标签(动态查询)

###### if

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/if%E6%A0%87%E7%AD%BE.PNG)

###### choose-when-otherwise

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/switch_case%E6%A0%87%E7%AD%BE.PNG)

###### update-set

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/set%E6%A0%87%E7%AD%BE.PNG)

###### foreach

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/foreach%E6%A0%87%E7%AD%BE.PNG)



###### xml 逻辑符号

```java
原符号       <        <=      >       >=       &        '        "
替换符号    &lt;    &lt;=   &gt;    &gt;=   &amp;   &apos;  &quot;
```



#### 一级缓存

默认开启，作用范围是本地缓存。eg：sqlsession从开启到关闭。

缓存失效：

1.查询不同的东西

2.增删改文件必定刷新缓存

3.查询不同的mapper.xml(这个连二级缓存都会失效)

4.手动清理缓存



#### 二级缓存

需要手动开启

在mapper.xml中加入

```xml
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

在mybatis-config.xml中加入

```xml
<!--开启二级缓存-->
<settings>    
     <setting name="cacheEnabled" value="true"/>
</settings>
```

==注意==：在多表查询时候，二级缓存是有很大风险的。在两个不同的mapper中都涉及到同一个表的增删改查操作，当其中一个mapper对这张表进行查询操作，此时另一个mapper进行了更新操作刷新缓存，然后第一个mapper又查询了一次，那么这次查询出的数据是脏数据。出现脏读的原因是他们的操作的缓存并不是同一个。

### 微服务

> 什么是微服务

官方定义：微服务架构是一种将单应用程序作为一套小型服务开发的方法，每种应用程序都在其自己的进程中运行，并与轻量级机制（通常是HTTP资源的API）进行通信。这些服务是围绕业务功能构建的，可以通过全自动部署机制进行独立部署。这些服务的集中化管理已经是最少的，它们可以用不同的编程语言编写，并使用不同的数据存储技术。

通俗定义：在父目录下每一个子目录都实现具体的一个功能，对于整个项目来说，这里的每一个springboot都是一个微服务。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E5%BE%AE%E6%9C%8D%E5%8A%A1%E9%80%9A%E4%BF%97%E5%AE%9A%E4%B9%89.PNG)

### 分布式

> 什么是分布式

把一件大任务拆分成一些小任务。

### 高可用

> 什么是高可用

对于某一个服务，如果其中某个环节出现了问题，由于此环节不止单人负责，剩下的人能够继续完成此环节，所以即使会影响一点点性能，但是整体仍然能够正常的运作。高可用通常是通过集群的布置来实现的。



### maven依赖、config配置文件

### springcloud - restfull（RestTemplate）

#### 微服务管理（eureka、zookeeper）

#### 负载均衡（ribbon、feign）

### 并发处理手段（hystrix和dubbo：熔断、降级、限流）

#### 消息队列MQ（kafka、消息中间件、RabbitMQ、ActiveMQ）

### 缓存（memcache）

### 搜索（elasticseach）



# *数据库*

### mysql

###### 常规操作

> 表字段操作

```sql
--使用数据库
USE 数据库名
--修改表名
ALTER TABLE department1 RENAME AS department
--增加表字段
ALTER TABLE department ADD test1 VARCHAR(50)
--修改字段属性
ALTER TABLE department MODIFY test VARCHAR(30) --修改约束
ALTER TABLE department CHANGE test2 test1 INT(10) --修改表名(也可修改约束)
--删除表字段
ALTER TABLE department DROP test1
--删除表
DROP TABLE IF EXISTS USERS
--添加外键
ALTER TABLE main1 ADD CONSTRAINT FK_name FOREIGN KEY (NAME) REFERENCES foreign1(NAME);--物理外键，尽量避免使用
--清空表数据
TRUNCATE foreign1 --与delete区别：①会重置自增计数器 ②不会影响事务
```

删除外键时必须先删除从表(含有外键的表)，否则会报错。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E5%A4%96%E9%94%AE%E6%8A%A5%E9%94%99.PNG)

> 查询总语法

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/select%E8%AF%AD%E6%B3%95.png)

> 去重 ->distinct

```sql
--某一列不重复
SELECT DISTINCT info FROM foreign1
--根据某一列不重复查所有
SELECT DISTINCT * FROM foreign1 GROUP BY info
```

> 模糊查询->like

```sql
--查询‘祝’开头的
SELECT * FROM foreign1 WHERE NAME LIKE '祝%'
--查询‘祝’开头后面只有一个字的
SELECT * FROM foreign1 WHERE NAME LIKE '祝_'
--查询‘祝’开头后面只有两个字的
SELECT * FROM foreign1 WHERE NAME LIKE '祝__'
--查询包含祝字的
SELECT * FROM foreign1 WHERE NAME LIKE '%祝%'
```

> 模糊查询 ->in

```sql
--查询名字是（x1，x2，x3）中的数据
SELECT * FROM foreign1 WHERE NAME IN ('祝一','一祝一','祝二')
```

> 联表查询 ->join on
>

```sql
--返回所有交集
SELECT m.id,f.name FROM main1 m INNER JOIN foreign1 f ON f.name = m.name
--返回所有右表中存在的值，即使左表中没有
SELECT m.id,f.name FROM main1 m RIGHT JOIN foreign1 f ON f.name = m.name
--返回所有左表中存在的值，即使右边中没有
SELECT m.id,f.name FROM main1 m LEFT JOIN foreign1 f ON f.name = m.name
--三表，根据具体需要留全哪边的信息选择左右联表
SELECT f1.name,f1.info,f1.inc,f2.info,m.id FROM foreign1 f1
INNER JOIN foreign2 f2 ON f1.name = f2.name 
INNER JOIN main1 m ON f1.name = m.name
```



> 自查询(嵌套)

```sql
SELECT id,NAME FROM main1 WHERE NAME=(SELECT NAME FROM foreign1 WHERE NAME = '祝一')
```



> 自连接 ->将一张表看作两张表

```sql
SELECT f.name '父课程',s.name '子课程' FROM myself f,myself s WHERE f.id = s.pid
```

原表：

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E8%87%AA%E6%9F%A5%E8%AF%A2%E5%8E%9F%E8%A1%A8.PNG)

结果：

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E8%87%AA%E6%9F%A5%E8%AF%A2%E7%BB%93%E6%9E%9C.PNG)

> 排序

```sql
--升序
SELECT * FROM myself ORDER BY pid ASC
--降序
SELECT * FROM myself ORDER BY pid DESC
```



> 分页

```sql
--起始，页面大小
SELECT * FROM myself LIMIT 0,5
```



###### 函数和运算符

> 函数见官方文档
>
> eg:一般数学运算、日期、字符串
>
> 获取当前日期：CURDATE()
>
> 获取当前时间：NOW()

[https://dev.mysql.com/doc/refman/5.7/en/functions.html]: https://dev.mysql.com/doc/refman/5.7/en/functions.html

> 用法

select 函数；

eg：

```sql
--查询x表中所有姓祝的同学并改姓朱
SELECT REPLACE(NAME,'祝','朱') FROM X WHERE NAME LIKE('祝%')    
```



> 聚合函数

```sql
--COUNT 有主键效率：1>3>2
SELECT COUNT(info) FROM foreign2 --会忽略所有的null值
SELECT COUNT(*) FROM foreign2 --不会忽略所有的null值
SELECT COUNT(1) FROM foreign2 --不会忽略所有的null值

SELECT SUM(result) FROM grade --总和
SELECT AVG(result) FROM grade --平均
SELECT MAX(result) FROM grade --最大
SELECT MIN(result) FROM grade --最小

--聚合函数条件
SELECT SUM(result) 总分,AVG(result) 平均分,MAX(result) 最高分,MIN(result) 最低分 FROM grade GROUP BY id HAVING 平均分>=80
--非聚合函数条件(对比)
SELECT SUM(result) 总分,AVG(result) 平均分,MAX(result) 最高分,MIN(result) 最低分 FROM grade WHERE result>=80
```

### Innodb  <->Myisam

Innodb：支持事务、采用行锁

### 事务

> ACID原则

- 原子性(atomicity)：事务要么全部提交成功，要么全部提交失败
- 一致性(consistency)：数据库数据从一个合法状态到另一个合法状态。包括总量不变，数据满足约束条件。
- 隔离性(Isolation)：多个事务并发时，事务之间是隔离开的，无法查看和影响中间数据。
- 持久性(durability)：在事务完成以后，该事务所对数据库所作的更改便持久的保存在数据库之中，并不会被回滚。

### 并发事务(隔离性)带来的几个问题：

###### ``脏读``

事务A读取了事务B中尚未提交的数据。如果事务B回滚，则A读取使用了错误的数据。

解决：如果 一个事物在读的时候，禁止读取未提交的事物。

###### ``幻读``

多次查询事务中的某个数据，在查询间隔，被另一个事务修改（指insert和delete操作）并提交了。

###### ``不可重复读``

多次查询事务中的某个数据，在查询间隔，被另一个事务修改（指update操作）并提交了。

###### ``更新丢失``

**回滚丢失**：A,B同时操作一个数据，A先提交后，B回滚导致A操作失效

**覆盖丢失**：A,B同时操作一个数据，A先提交，然后B提交，导致A的提交失效

### 事务隔离级别：

###### ``未提交读``

事务中的修改，即使没有提交，对其它事务也是可见的。

###### ``提交读``

一个事务只能读取已经提交的事务所做的修改。换句话说，一个事务所做的修改在提交之前对其它事务是不可见的。

###### ``可重复读``

保证在同一个事务中多次读取同样数据的结果是一样的。

###### ``可序列化``

强制事务串行执行。(所有行加锁)

![image-20200924205617753](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200924205617753.png)

> 数据库内事务操作

```sql
SET autocommit = 0;-- 关闭事务自动提交
START TRANSACTION 
-- 操作1
-- 操作2
COMMIT -- 提交事务
ROLLBACK -- 回滚，如果1、2操作都执行但是没有提交则回滚会操作前状态,如果已经提交根据持久性原则则不会提交
SET autocommit = 1; -- 开启事务自动提交
```

### 索引

注意：索引会提高查询速度，但是会降低添加、删除和更新的速度，因为还得更新索引信息。

###### 使用：

B+树（依赖于最左匹配原则）

###### 不使用：

hash：部分查找和范围查找(部分查找：hash(id+name)=key,如果我只输入id不能查找)，但是hash是最快的。

红黑树：树高（I/O多），分页（浪费磁盘空间，一页存的数据太少）

B树：范围查找时会出现索引失效的现象（需要遍历）

> 定义

索引（Index）是帮助MySQL高效获取数据的数据结构。提取句子主干，就可以得到索引的本质：索引是数据结构。

> 分类

- 主键索引（primary key）
  - 不可重复，一个表只能有一个主键
- 唯一索引（unique key）
  - 避免列属性重复(eg：name列有唯一索引，在里面已经有“张三”了，再添加张三就会报错)
- 常规索引（key/index）
  - 默认索引
- 全文索引 （fulltext）

> 索引添加及效率比较

```sql
-- 用函数插入十万条数据测试
DELIMITER $$ -- 定义一个函数结束符

CREATE FUNCTION testdata()
RETURNS INT
BEGIN

DECLARE number INT DEFAULT 100000;
DECLARE i INT DEFAULT 0;
WHILE i<number DO

SET i = i+1;
INSERT INTO indextest VALUES(UUID(),CONCAT('用户',i));

END WHILE;
RETURN i;
END $$

SELECT testdata(); -- 调用函数

SELECT * FROM indextest WHERE NAME = '用户99999'; -- 没索引 0.026 sec
EXPLAIN SELECT * FROM indextest WHERE NAME = '用户99999'; -- 查询结果见下图，查了99806条数据

CREATE INDEX id_indextest_name ON indextest(NAME); -- 给name列添加常规索引

SELECT * FROM indextest WHERE NAME = '用户99999'; -- 有索引 0 sec
EXPLAIN SELECT * FROM indextest WHERE NAME = '用户99999'; -- 查询结果见下图，查了1条数据
```

无索引查询：

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E6%97%A0%E7%B4%A2%E5%BC%95%E6%9F%A5%E8%AF%A2.PNG)

有索引查询：

![image-20201214152132889](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20201214152132889.png)

### explain操作

``参数``

id:选择标识符（越大越先）
select_type:表示查询的类型。
table:输出结果集的表
partitions:匹配的分区
type:表示表的连接类型
possible_keys:表示查询时，可能使用的索引
key:表示实际使用的索引
key_len:索引字段的长度
ref:列与索引的比较
rows:扫描出的行数(估算的行数) -----一般看这条
filtered:按表条件过滤的行百分比
Extra:执行情况的描述和说明



### 数据库设计规范

> 三大范式

1NF：列属性满足原子性，不可再分。



![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E7%AC%AC%E4%B8%80%E8%8C%83%E5%BC%8F%E5%88%86%E6%9E%90.PNG)

2NF：满足第一范式，且每张表只描述一件事情。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E7%AC%AC%E4%BA%8C%E8%8C%83%E5%BC%8F%E5%88%86%E6%9E%90.PNG)

3NF：满足一二范式，且非主键信息与主键直接相关

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E7%AC%AC%E4%B8%89%E8%8C%83%E5%BC%8F%E5%88%86%E6%9E%90.PNG)

**但是有时候为了提高性能，减少联表查询，提高性能，需要故意增加一些冗余字段（阿里要求关联查询不可超过3张表）**



> 数据库设计步骤（以博客为例）

1. 收集信息，分析需求
   - 用户表（用户登陆注销，个人信息，写博客，创建分类）
   - 分类表（文章分类，创建者）
   - 文章表（文章信息）
   - 评论表
   - 友链表（友链信息）
   - 自定义表（放一些零散信息，一般为key：value形式），**非必须**
2. 标识实体（将需求落实为数据库）
3. 根据所需功能列出每项功能所要关联的表，检查表格信息是否完善，eg：
   - 写博客：user->blog（关联两个表）
   - 关注：user->user（同一个表的不同数据）
   - 友链：links(单独一个表)
   - 评论：commit->user->blog(多个表)·



## *redis*

### 数据类型

##### 1.常规操作

```java
ping //检验是否连通(连通则返回pong)
flush db//（清除单数据库）
flushall//（清除所有数据库）
select db2 //选择数据库
incr x//自增1
decr x//自减1
incrby x 10//加10
decrby x 10//减10
ttl//查询过期时间（若过期则返回-2）
keys//查询所有key
clear//清除
auth 密码 //验证密码
redis-server conf文件 //开启redis服务
redis-cli -p 端口 //连接redis服务
shutdown //断开连接
exit //退出
```



#### 2.String操作

```java
set k1 v1 //设置
get k1	//获取
del k1 //删除

getset k1 str//先获取k1的值再设置k1为str

mset user:1:name zss user:1:age 22//群体赋值
set user:1{name:zss,age:22}//与上一行等价(json)

type k1 //获取k1的数据类型
exists k1 //判断k1是否存在，若存在返回1
strlen k1//获取字符串长度
append k1 "str"//追加字符串str，若k1不存在则等于新建
getrange k1 begin end//从k1中截取索引begin到end的一段，若end是-1则等于get
setrange k1 index str//替换index索引处value为str
setex k1 10 str//设置k1为str，10s后失效
setnx k1 str//如果k1不存在设设置为str,存在则失败
```

``应用``

①常规用法



#### 3.List操作

```java
lpush list str//左添加
rpush list str//右添加
lrange begin end //左边从begin到end的value值，end为-1表示获取全部
lpop list//左查询并移除
rpop list//右查询并移除
lindex k1 index //通过索引查询单个
llen list //返回列表长度
lrem list count value //移除list中count个值为value的成员
ltrim list begin end //截取从begin到end的一段
lset list index value //修改index索引处值为value（不自动创建）
linsert list before/after value1 value2 //在value1前/后添加value2
```

``应用``

①消息队列（灵活实现队列和栈）



#### 4.Set操作（数据不重复）

```JAVA
sadd k1 str //添加
srem set value //移除value
smember set //获取set中值
sismenbers set value //判断value是不是set中值，是1否0
scard set  //获取set中元素个数
srandmember set //从set中随机取值
spop //查看并移除第一个元素
smove set1 set2 value //把set1中的value移动到set2中
//重要：交并差
sdiff set1 set2 //set1-set2 set1中有set2没有的
sinter set1 set2 //求set1和set2的交集
sunion set1 set2 //求set1和set2的并集
```

``应用``

①共同关注

②推荐好友



#### 5.zset（排序的set）

```java
zadd zset 位置1 score1 位置2 score2
zrem zset score //移除指定元素
zrange zset 0 -1 //查所有元素
zcard zset //查所有元素个数
zcount zset score1 score2 //获取指定区间的元素个数
zrangebyscore zset -inf +inf(with scores) //根据score正序查看(带score) -inf和+inf为区间
zrevrangebyscore zset +inf -inf(with scores) //根据score倒序查看(带score) -inf和+inf为区间
```

``应用``

①排行榜

②数据加权



#### 6.hash操作（key-map）

```java
//用法与string类似，set→hset，区别在于string一个key对应一个value，hash一个key对应一个<key,value>map集合
hgetall hash //获取hash中所有数据
hlen //获取hash长度
hkeys hash //获取hash中所有key
hvals hash //获取hash中所有value
```

``应用``
①若某一个对象中的单个或者少量数据经常发生变动，hash比string更适合存储



#### 6.hyperloglog（基数(不重复数)统计）

```java
PFADD hl a b c d e f g //添加数据
PFADD h2 a b c d e f g 
PFCOUNT hl //统计基数
PFMERGE h3 h1 h2 //将h1和h2合并成h3
```

``应用``

①统计用户数量（如果用set保存，再统计数量则很浪费空间！hyperloglog固定内存空间12k，但有0.81%错误率）



#### 7.geospatail（地理位置，注：底层是zset）

```java
//由于底层是zset，所以可以用zset的操作来操作geo
GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania" //往sicily中添加城市经纬度信息(先经后纬)
(integer) 2
redis> GEODIST Sicily Palermo Catania //查询两地距离
"166274.1516"
redis> GEORADIUS Sicily 15 37 100 km //查询，15和37为给定的经度和纬度
1) "Catania"
redis> GEORADIUS Sicily 15 37 200 km
1) "Palermo"
2) "Catania"
    
GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania"
(integer) 2
redis> GEOPOS Sicily Palermo Catania NonExisting //返回指定地点的经纬度（不存在返回nil）
1) 1) "13.36138933897018433"
   2) "38.11555639549629859"
2) 1) "15.08726745843887329"
   2) "37.50266842333162032"
3) (nil)
    
redis> GEOADD Sicily 13.583333 37.316667 "Agrigento"
(integer) 1
redis> GEOADD Sicily 13.361389 38.115556 "Palermo" 15.087269 37.502669 "Catania"
(integer) 2
redis> GEORADIUSBYMEMBER Sicily Agrigento 100 km //查询Agrigento地点方圆100km的城市
1) "Agrigento"
2) "Palermo"
    
GEOHASH Sicily Palermo Catania //返回某地的hash表示
```

``应用``

①附近的人/美食/etc..



#### 8.bitmap（位存储：2个状态0 1）

```java
setbit flag day 1/0 //设置flag中day属性的位图标志为0或者1
bitcount //统计位图标记为1的数量
```

``应用``

①打卡

②在线用户统计

③判断活跃用户



### ==事务----乐观锁==

**性质**：一次性、顺序性、排他性;     不保证原子性！当其中部分指令失效，其他指令继续执行。

**multi** : 标记一个事务块的开始（ queued ）

**exec** : 执行所有事务块的命令 （ 一旦执行exec后，之前加的监控锁都会被取消掉 ）　

**discard** : 取消事务，放弃事务块中的所有命令

**两种错误方式**

①若其中有编译错误，则全部不执行！

②若其中有逻辑错误（如某条指令对string加一），则只有错误指令不执行！

**使用watch监视**

案例一：使用watch检测balance，事务期间balance数据未变动，事务执行成功

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/redis%E4%BA%8B%E5%8A%A11.PNG)

案例二：使用watch检测balance，在开启事务后（标注1处），在新窗口执行标注2中的操作，更改balance的值，模拟其他客户端在事务执行期间更改watch监控的数据，然后再执行标注1后命令，执行EXEC后，事务未成功执行。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/redis%E4%BA%8B%E5%8A%A12.PNG)

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/redis%E4%BA%8B%E5%8A%A13.PNG)

**一但执行 EXEC 开启事务的执行后，无论事务使用执行成功， WARCH 对变量的监控都将被取消。**

**故当事务执行失败后，需重新执行WATCH命令对变量进行监控，并开启新的事务进行操作。**

### ==配置文件==

###### 1.单位

![image-20200928212414107](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928212414107.png)

###### 2.网络

<img src="https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928212546836.png" alt="image-20200928212546836" style="zoom:67%;" />

![image-20200928212627137](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928212627137.png)

###### 3.通用 GENERAL

①是否守护进程开启(默认为no，一般开启yes)

<img src="https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928212701861.png" alt="image-20200928212701861"  />

②如果以守护进程开启，则需要指定pid文件位置

![image-20200928212923326](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928212923326.png)

③日志级别

![image-20200928212945755](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928212945755.png)

④日志文件名

![image-20200928213019244](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928213019244.png)

⑤设置数据库个数

![image-20200928213054670](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928213054670.png)



⑥设置密码(在后面添加 requirepass 新密码)

![image-20200928213720016](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928213720016.png)

###### 4.内存

![image-20200928214142587](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928214142587.png)

![image-20200928214151715](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928214151715.png)

![image-20200928214215293](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928214215293.png)（内存满后策略）

###### 5.rdb持久化

①rdb持久化

![image-20200928213226785](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928213226785.png)

②持久化出错是否继续工作

![image-20200928213346341](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928213346341.png)

③是否压缩rdb文件

![image-20200928213432464](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928213432464.png)

④本地rdb文件名

![image-20200928213529317](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928213529317.png)

⑤rdb文件保存路径

![image-20200928213558877](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928213558877.png)

###### 6.aof持久化

①aof模式默认不开启![image-20200928214520694](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928214520694.png)

②默认aof文件名

![image-20200928214601222](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928214601222.png)

③同步频率

![image-20200928214647513](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/image-20200928214647513.png)

### ==发布订阅==

**概念：**

　　Redis消息订阅发布是进程间的一种消息通信模式，发送者pub发送消息，订阅者sub接收消息。

**使用须知：**

　　**需要先订阅后发布，才能接收到消息。在订阅时，相当于创建了可供发布的频道**。在订阅某个频道后若频道发布某条消息则会立即显示：显示内容依次为：①消息名 ②频道 ③消息内容

**案例：**

**（1）订阅频道**

　　订阅单个：　subscribe new

　　订阅多个：　subscribe game video images

　　使用通配符订阅多个： pubscribe new* （将订阅所有已new开头的频道推送消息）

**（2）消息发布**

　　　　publish new hello-redis



### ==主从机制（哨兵模式）==

**信息查看**

```bash
info replication #主要查看是否主/从机
```

**概念：**

　　主从复制就是主机数据更新后，根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主。

#### **（1）“一主二仆”**

　　准备三台redis服务器：主服务器A，从服务器B1、从服务器B2。服务器B1、B2同步A数据，A1负责写操作，B1、B2负责读操作。

　　A服务器IP：168.7.5.74，端口6379

　　B1服务器IP：168.7.5.75，端口6379

　　B2服务器IP：168.7.5.76，端口6379

- **使用命令**

　　在服务器B1、B2上分别执行如下命令，成为A服务器的从库：

　　*slaveof 168.7.5.74 6379*

　　**注意：**　

　　　　a、当主库宕机后，从库仍是slave角色，仍负责接受读操作；

　　　　b、当主库恢复服务后，从库B1、B2继续同步主库A的数据，服务照旧；

　　　　c、当从库与master断开连接后，如果使用的是命令配置，需要重新使用命令才能连接到主库；

　　　　d、当从库连接master后，master会先一次性将当前数据全量同步到salve上，后续增量同步。

　　**从库复制原理：**

　　　　Slave 启动成功后连接到 master 后，将发送一条sync命令，master接收到命令后，在后台启动存盘进程，同时收集所有接收到的用于修改数据集的命令，在后台进程执行完毕后，master将传送整个数据文件到slave，以完成一次全量同步。只要重新连接master，将自动执行一次**全量复制**（完全同步）。

#### **（2）“薪火相传”**

　　准备三台redis服务器：服务器A，服务器B、服务器C。将服务器B设置为服务器A的从库，将服务器C设置为服务器B的从库：masterA <-- slaverB <-- slaverC。　　

　　A服务器IP：168.7.5.74，端口6379

　　B服务器IP：168.7.5.75，端口6379

　　C服务器IP：168.7.5.76，端口6379

　　执行如下命令进行该策略配置：

　　在服务器B上执行：*slaveof 168.7.5.74 6379*

　　在服务器C上执行：*slaveof 168.7.5.75 6379*

　　**注意：**

　　　　a、服务器A的角色为 master，服务器B和C的角色为 slaver；

　　　　b、当主库A宕机后，在从库B上执行命令 *slaveof no one* 将从库B角色变为 salve ，从库C无需操作，将继续使用同步B；

　　　　c、当A重启服务器后，与BC库组成的体系毫无关系，需重新执行命令加入；

　　　　d、**salveof no one ：是当前数据库停止与其他数据库的同步，并转换为主数据库。**

　　优点：去除“一主二仆”策略的中心化，减轻master库写的压力。

　　缺点：从库存在备份延迟

#### **（3）==哨兵模式==**

​		==哨兵是一个独立监控进程！==

　　**原理：**在后台自动监控主机是否故障(不断发送请求)，如果故障了，根据**投票**自动将从库转为主库。

　　 **配置：**

　　 a、创建哨兵模式配置文件。在redis.conf目录下创建文件 **sentinel.conf** (名称固定)，内容如下：



　　　　**==*sentinel monitor hostname 127.0.0.1 6379 1*==** 



　　　　hostname 127.0.0.1 6379 ：分别是被监控主机的**主机名、IP及端口号**

　　　　**1指定策略：主机宕机后，slave投票决定谁接替主机**

　　  b、启动哨兵模式

　　　　 *redis-sentinel /usr/redis/sentinel.conf*

　　**注意：**

　　　　a、若当前master宕机后，服务将在从库中选出新的master；

　　　　b、若旧master重启后，它将自动成为新的master的slave；//主机回来只能当小弟！！

　　　　c、一个哨兵(sentinel)可以监控多个master。

 **手动配置conf文件修改步骤**：

1.修改端口

2.修改pid

3.修改log文件名字

4.修改dump.rdb名字

**注：通过命令配置的主从复制只是暂时的，服务关闭则取消；若要持久则需要手动修改conf文件**(replicaof ip 端口)

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/config%E6%8C%81%E4%B9%85%E9%85%8D%E7%BD%AE%E4%B8%BB%E4%BB%8E.png)

**应用：**

　　读写分离、故障恢复



### ==持久化==

==若rdb和aof同时开启，则系统优先使用aof==

#### rdb

**（保存的是二进制文件，默认持久化方式）**

**原理**：在执行持久化时，redis使用Fork的方式单独创建一个进程来进程持久化，该进程会先将内存中的数据写入到一个临时文件中，待持久化结束时，用此临时文件替换掉上次持久化保存的文件

**概念**：在指定的时间间隔内将内存中的数据集快照（Snapshot）写入磁盘，待服务重启时，将快照文件读入内存中。

**触发条件**：

1.满足save规则

2.执行flushall

3.退出redis

**恢复rdb**：将rdb文件放在redis的启动目录下，会自动启用

**若数据异常**：使用命令 ***redis-check-dump --fix*** ***<filename>*** 命令对 dump.rdb 持久化文件进行修复，然后重启redis服务即可

**触发条件**：满足同步频率刷新

**优点：**持久化过程中，主进程不进行任何IO操作，对于大规模的数据恢复，具有极高的性能。

**缺点：**RDB最后一次持久化后的数据可能会因为redis意外down掉而丢失最后一次快照后修改的所有数据；
　　Fork时，内存数据被克隆，大致2倍的膨胀性，会对服务器造成一定的压力，影响性能。　　　

**应用：**适合大规模数据恢复，且对恢复数据的完整性、一致性不是很严格，这种情况下RDB比AOF更加高效。



#### aof

**（追加文件，保存的是日志文件）**

**原理：**以日志的形式来记录每个写操作（读操作不记录），只许追加文件，不可改写文件，redis启动时，将根据日志文件内容将写指令**从前到后执行一次**以完成数据的恢复。

**若数据异常**：若 AOF文件存在异常，数据恢复将报错。可使用命令 ***redis-check-aof --fix <filename>*** 命令对 .aof 持久化文件进行修复，然后重启redis服务即可。

**优点：**可以设置appendfsync 为 每秒同步 每次修改同步 和不同步，最恶劣情况下，丢失不超过两秒的数据

**缺点：**相同数据集的数据，AOF文件远大于RBD文件，且恢复速度慢于RBD；AOF运行效率慢于RBD，每秒同步策略效率较好，不同步效率和RDB相同；AOF在rewrite过程中将新数据写到新文件中造成的阻塞是不可避免的。



### ==常见异常==

###### ==缓存穿透==

原理：查询redis中没有值，于是会“穿透”缓存层到mysql层查询，若有恶意查询或者遇到秒杀业务，可能造成数据库崩溃

解决：

①若redis中不存在，则置空（此法不好，可能出现大量空值，浪费内存）

②布隆过滤器BloomFilter（在redis前再加一层），对所有可能查询的参数以hash形式存储，当用户想要查询的时候，使用布隆过滤器发现不在集合中，就直接丢弃，不再对持久层查询。

 

###### ==缓存击穿==

原理：某个key非常热门，但是它突然失效，这时候所有查询走数据库，导致压力过大

解决：

①热点数据永不过期

②加互斥锁（分布式锁）保证同一时间只有一个查询



###### ==缓存雪崩==

原理：缓存雪崩表示在某一时间段，缓存集中失效，导致请求全部走数据库。

解决：

①若是人为因素(断电..)，则可以通过==哨兵模式==做预防，或者设置二级缓存。

②若是key集体失效问题，则可以对key失效时间加一个随机值。

==高可用常见手段==：==服务降级==(从客户端角度停掉一些服务，偏整体)，==服务熔断==(从服务器角度停掉一些服务，偏局部)，==数据预热==(把可能的热点数据先访问一遍，先放入缓存)，==限流操作==(限制总并发数、瞬时并发数等)。



###### ==布隆过滤器==

...........................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................原理：本质上是一个bit数组，通过1标记存在数据，为解决hash冲突带来的覆盖问题，会使用多个hash值对应一个数据，导致：布隆过滤器查询存在的值可能是不存在的，但是其查询不存在的值一定是不存在的。

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A81.PNG)

![](https://raw.githubusercontent.com/mountainsZhu/TyporaImage/main/img/%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A8%E5%8E%9F%E7%90%86.PNG)

图解：一个数据hash函数返回1、4、7；另一个hash函数返回3、4、8，最后1,3,4,7,8为1，hash算出来在这中间的都表示可能存在(但是实际可能是别的数据的hash值，故而可能不存在)，但是如果算出来都不再这几个当中，name一定是不存在的。



**给redis安装布隆过滤器：**

**1、下载：**

地址：https://github.com/RedisBloom/RedisBloom

下载ZIP 文件，上传到linux

RedisBloom-master.zip

**2、解压编译**

 命令：

 unzip RedisBloom-master.zip

 cd RedisBloom-master

 make

 扫行完以上命令 后文件夹内生成一个文件名为：redisbloom.so

 

## 3、启动redis 时加载该模块

 命令：

```
 redis-server redis-6381.conf --loadmodule /zjl/software/RedisBloom-master/redisbloom.so 
```



###### ==分布式锁==

- **加锁**：使用setnx命令，伪代码：setnx(id,value)
    返回1，说明key不存在，线程抢锁成功；
      返回1，说明key已存在，线程抢锁失败。
     注意：setnx(id,value)中key为操作商品的id，value值可用于防止误删锁，下文有提到。
- **解锁**：使用del命令，伪代码：del(id)
- **锁超时**：如果一个得到锁的线程在执行任务的过程中挂掉，来不及显式地释放锁，该资源将会被永远占用，其他线程将无法访问。可以使用expire为key设置一个超时时间，与setnx命令一起执行（setnx不支持超时参数），用以保证即使未被显式释放，该锁也可在一定时间后自动释放。伪代码：expire（key， 30）。
- 若想保证操作的原子性，则可以用set代替setnx和expire、伪代码：set（key, value, expire）。



### ==java代码实现==

#### **jedis**

```java
Jedis jedis = new Jedis("ip",端口);
jedis.基本指令
```



#### **springboot集成redis**

配置完成后：

```java
private RedisTemplate redisTemplate;
redisTemplate.接口;
```



```java
//配置自己的RedisTemplate
@Configuration
public class RedisConfig {
    //编写自己的配置类
    @Bean
    @SuppressWarnings("all")//忽略所有警告
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory){
        //为了开发方便，直接使用String, Object
        RedisTemplate<String, Object> template = new RedisTemplate<String,Object>();
        template.setConnectionFactory(redisConnectionFactory);

        //json序列化配置
        Jackson2JsonRedisSerializer<Object> objectJackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<Object>(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        //om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        om.activateDefaultTyping(LaissezFaireSubTypeValidator.instance,ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.PROPERTY);
        objectJackson2JsonRedisSerializer.setObjectMapper(om);

        //String的序列化
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();

        //配置具体的序列化方式
        //key采用String的序列化方式
        template.setKeySerializer(stringRedisSerializer);
        //hashkey也采用String序列化方式
        template.setHashKeySerializer(stringRedisSerializer);
        //value采用jeckson序列化方式
        template.setValueSerializer(objectJackson2JsonRedisSerializer);
        //hashValue也采用jeckson序列化方式
        template.setHashValueSerializer(objectJackson2JsonRedisSerializer);

        //set完成后将所有的配置设置进去
        template.afterPropertiesSet();
        return template;
    }
}

```



```java
package com.redis.springboot.Util;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Component;
import org.springframework.util.CollectionUtils;

import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.TimeUnit;

@Component
public final class RedisUtil {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    // =============================common============================
    /**
     * 指定缓存失效时间
     * @param key  键
     * @param time 时间(秒)
     */
    public boolean expire(String key, long time) {
        try {
            if (time > 0) {
                redisTemplate.expire(key, time, TimeUnit.SECONDS);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据key 获取过期时间
     * @param key 键 不能为null
     * @return 时间(秒) 返回0代表为永久有效
     */
    public long getExpire(String key) {
        return redisTemplate.getExpire(key, TimeUnit.SECONDS);
    }


    /**
     * 判断key是否存在
     * @param key 键
     * @return true 存在 false不存在
     */
    public boolean hasKey(String key) {
        try {
            return redisTemplate.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除缓存
     * @param key 可以传一个值 或多个
     */
    @SuppressWarnings("unchecked")
    public void del(String... key) {
        if (key != null && key.length > 0) {
            if (key.length == 1) {
                redisTemplate.delete(key[0]);
            } else {
                redisTemplate.delete(CollectionUtils.arrayToList(key));
            }
        }
    }


    // ============================String=============================

    /**
     * 普通缓存获取
     * @param key 键
     * @return 值
     */
    public Object get(String key) {
        return key == null ? null : redisTemplate.opsForValue().get(key);
    }

    /**
     * 普通缓存放入
     * @param key   键
     * @param value 值
     * @return true成功 false失败
     */

    public boolean set(String key, Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 普通缓存放入并设置时间
     * @param key   键
     * @param value 值
     * @param time  时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */

    public boolean set(String key, Object value, long time) {
        try {
            if (time > 0) {
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            } else {
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 递增
     * @param key   键
     * @param delta 要增加几(大于0)
     */
    public long incr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }


    /**
     * 递减
     * @param key   键
     * @param delta 要减少几(小于0)
     */
    public long decr(String key, long delta) {
        if (delta < 0) {
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }


    // ================================Map=================================

    /**
     * HashGet
     * @param key  键 不能为null
     * @param item 项 不能为null
     */
    public Object hget(String key, String item) {
        return redisTemplate.opsForHash().get(key, item);
    }

    /**
     * 获取hashKey对应的所有键值
     * @param key 键
     * @return 对应的多个键值
     */
    public Map<Object, Object> hmget(String key) {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * HashSet
     * @param key 键
     * @param map 对应多个键值
     */
    public boolean hmset(String key, Map<String, Object> map) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * HashSet 并设置时间
     * @param key  键
     * @param map  对应多个键值
     * @param time 时间(秒)
     * @return true成功 false失败
     */
    public boolean hmset(String key, Map<String, Object> map, long time) {
        try {
            redisTemplate.opsForHash().putAll(key, map);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 向一张hash表中放入数据,如果不存在将创建
     *
     * @param key   键
     * @param item  项
     * @param value 值
     * @param time  时间(秒) 注意:如果已存在的hash表有时间,这里将会替换原有的时间
     * @return true 成功 false失败
     */
    public boolean hset(String key, String item, Object value, long time) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 删除hash表中的值
     *
     * @param key  键 不能为null
     * @param item 项 可以使多个 不能为null
     */
    public void hdel(String key, Object... item) {
        redisTemplate.opsForHash().delete(key, item);
    }


    /**
     * 判断hash表中是否有该项的值
     *
     * @param key  键 不能为null
     * @param item 项 不能为null
     * @return true 存在 false不存在
     */
    public boolean hHasKey(String key, String item) {
        return redisTemplate.opsForHash().hasKey(key, item);
    }


    /**
     * hash递增 如果不存在,就会创建一个 并把新增后的值返回
     *
     * @param key  键
     * @param item 项
     * @param by   要增加几(大于0)
     */
    public double hincr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, by);
    }


    /**
     * hash递减
     *
     * @param key  键
     * @param item 项
     * @param by   要减少记(小于0)
     */
    public double hdecr(String key, String item, double by) {
        return redisTemplate.opsForHash().increment(key, item, -by);
    }


    // ============================set=============================

    /**
     * 根据key获取Set中的所有值
     * @param key 键
     */
    public Set<Object> sGet(String key) {
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 根据value从一个set中查询,是否存在
     *
     * @param key   键
     * @param value 值
     * @return true 存在 false不存在
     */
    public boolean sHasKey(String key, Object value) {
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将数据放入set缓存
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSet(String key, Object... values) {
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 将set数据放入缓存
     *
     * @param key    键
     * @param time   时间(秒)
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSetAndTime(String key, long time, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().add(key, values);
            if (time > 0)
                expire(key, time);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 获取set缓存的长度
     *
     * @param key 键
     */
    public long sGetSetSize(String key) {
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 移除值为value的
     *
     * @param key    键
     * @param values 值 可以是多个
     * @return 移除的个数
     */

    public long setRemove(String key, Object... values) {
        try {
            Long count = redisTemplate.opsForSet().remove(key, values);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    // ===============================list=================================

    /**
     * 获取list缓存的内容
     *
     * @param key   键
     * @param start 开始
     * @param end   结束 0 到 -1代表所有值
     */
    public List<Object> lGet(String key, long start, long end) {
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 获取list缓存的长度
     *
     * @param key 键
     */
    public long lGetListSize(String key) {
        try {
            return redisTemplate.opsForList().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }


    /**
     * 通过索引 获取list中的值
     *
     * @param key   键
     * @param index 索引 index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
     */
    public Object lGetIndex(String key, long index) {
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     */
    public boolean lSet(String key, Object value) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 将list放入缓存
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     */
    public boolean lSet(String key, Object value, long time) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            if (time > 0)
                expire(key, time);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, List<Object> value) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }

    }


    /**
     * 将list放入缓存
     *
     * @param key   键
     * @param value 值
     * @param time  时间(秒)
     * @return
     */
    public boolean lSet(String key, List<Object> value, long time) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            if (time > 0)
                expire(key, time);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 根据索引修改list中的某条数据
     *
     * @param key   键
     * @param index 索引
     * @param value 值
     * @return
     */

    public boolean lUpdateIndex(String key, long index, Object value) {
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }


    /**
     * 移除N个值为value
     *
     * @param key   键
     * @param count 移除多少个
     * @param value 值
     * @return 移除的个数
     */

    public long lRemove(String key, long count, Object value) {
        try {
            Long remove = redisTemplate.opsForList().remove(key, count, value);
            return remove;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }

    }

}
```



# Linux操作系统

### 基本指令

```bash
ps -ef|grep redis #查询redis相关进程
rm -rf 文件名 #递归删除文件下的所有文件且不提示
```

