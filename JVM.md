[![CodeZX](http://47.98.150.21/upload/2020/3/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20200315164857-6f4e0b3fd29f4b8297f7d61e1b395972.jpg) CodeZX](http://47.98.150.21/)



# JVM学习笔记

张祥 2020年03月16日 2,730次浏览

## 1_介绍

### 01_什么是JVM

**定义**：java virtual meachine -java运行时环境（java二进制字节码的运行环境）。
**好处：**

1. 一次编写到处运行
2. 自动内存管理，垃圾回收
3. 数组下标越界检查
4. 多态

**比较：**

### 02_学习JVM有什么用？

- 面试
- 理解底层原理
- 中高级程序员必备技能

### 03_常见的JVM

![image.png](http://47.98.150.21/upload/2020/3/image-5dd332273c7c4966b4e35718bf60d305.png)

### 04_学习路线

![image.png](http://47.98.150.21/upload/2020/3/image-65e9271485834a4ea2dd05226b404ffc.png)

## 2_内存结构

### 01_程序计数器（线程私有）

**定义：** Program Counter Register程序计数器（寄存器）。
**作用：** 记住下一条jvm指令的执行地址。（这个介绍太简单了）。
**从字节码的执行原理来看，单线程的情况下程序计数器是可有可无的。因为即使没有程序计数器的情况下，程序会按照指令顺序执行下去，即使遇到了分支跳转这样的流程也会按照跳转到指定的指令处继续顺序执行下去，是完全能够保证执行顺序的。
但是现实中程序往往是多线程协作完成任务的。JVM的多线程是通过CPU时间片轮转来实现的，某个线程在执行的过程中可能会因为时间片耗尽而挂起。当它再次获取时间片时，需要从挂起的地方继续执行。在JVM中，通过程序计数器来记录程序的字节码执行位置。程序计数器具有线程隔离性，每个线程拥有自己的程序计数器。
[原文地址](https://blog.csdn.net/sunhuiliang85/article/details/90718251)**

![image.png](http://47.98.150.21/upload/2020/3/image-0edd2d23b7d54d0aab52c4836de0b4c7.png)

**特点**

- 线程私有，每个线程都有自己的计数器，与线程共存亡
- 内存中唯一一个不会出现内存溢出的区域
- 占据内存特别小

### 02_栈-线程栈（线程私有）

**定义：** Java Virtual Mechine Stacks(java虚拟机栈)

- 每个线程运行时所需要的内存空间
- 每个栈由多个**栈帧**组成，对应着该线程调用**方法占用的内存**
- 每个线程都只有一个**活动栈帧**，对应着线程当前执行的**方法**。

**问题：**

- 垃圾回收是否涉及到栈内存？
  **答：** 不会涉及。因为栈对应着一个程序的执行过程，在执行过程中，活动栈不断随着该方法的运行结束而弹出，直到程序执行完成，栈为空，消失。即一个进程执行完毕，线程栈消失。
- 栈内存越大越好吗？
  **答：** 不是。服务器物理内存是固定的，若栈内存越大，则能够同时运行的线程数量就会减小。栈是由多个栈帧组成，内存增大，只是将可以容纳栈帧的数量增多，除了可**以调用更多的方法(递归)**，没有其他的作用。**-Xss设置栈内存大小，一般-Xss=1M**。
- 线程的局部变量是否线程安全？
  **答：** 不一定。方法内的局部变量且没有逃离方法的作用访问时，是线程安全的。如果局部变量引用了对象，由于对象存在于堆中，一般其他线程可以访问修改，需要考虑线程安全。

**栈内存溢出**

- 栈帧过多，栈被撑破了（递归）
- 栈帧过大（交叉引用）

递归就不做演示了，调用自己即可。

**对象交叉引用**

```
package com.example.jvm;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;

import java.util.Arrays;
import java.util.List;

public class StackOverFlow {

    public static void main(String[] args) throws JsonProcessingException {
        Dept dept = new Dept();
        dept.setName("jndx");
        Emp e1 = new Emp();
        Emp e2 = new Emp();
        e1.setName("zx");
        e2.setName("hkw");
        e1.setDept(dept);
        e2.setDept(dept);
        dept.setEmp(Arrays.asList(e1,e2));
        ObjectMapper mapper = new ObjectMapper();
	//输出对象的Json字符串
        System.out.println(mapper.writeValueAsString(dept));
    }



}
class Dept{
    private String name;
    private List<Emp> emp;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public List<Emp> getEmp() {
        return emp;
    }
    public void setEmp(List<Emp> emp) {
        this.emp = emp;
    }



}
class Emp{
    private String name;
    private Dept dept;
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public Dept getDept() {
        return dept;
    }
    public void setDept(Dept dept) {
        this.dept = dept;
    }

} 
```

![image.png](http://47.98.150.21/upload/2020/3/image-be1e85a97ee54898807c3ea1d3a2936a.png)

```
Infinite recursion (StackOverflowError) (through reference chain: com.example.jvm.Emp["dept"]->com.example.jvm.Dept["emp"]->java.util.ArrayList[0]->com.example.jvm.Emp["dept"]->com.example.jvm.Dept["emp"]->java.util.ArrayList[0]->com.example.jvm.Emp["dept"]->com.example.jvm.Dept["emp"]->java.util.ArrayList[0]->com.example.jvm.Emp["dept"]->com.example.jvm.Dept["emp"]->java.util.ArrayList[0]->com.example.jvm.Emp["dept"]->com.example.jvm.Dept["emp"]->java.util.ArrayList[0]->com.example.jvm.Emp["dept"]->com.example.jvm.Dept["emp"]->java.util.ArrayList[0]->com.example.jvm.Emp["dept"]->com.example.jvm.Dept["emp"]->java.util.ArrayList[0]->com.example.jvm.Emp["dept"]->com.example.jvm.Dept["emp"]->java.util.ArrayList[0]->com.example.jvm.Emp["dept"]->com.example.jvm.Dept["emp"]->java.util.ArrayList[0]->com.example.jvm.Emp["dept"]-...... 
```

**交叉引用，导致数据过多，栈帧过大**

**使用ObjectMapper：**

```
 <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.8.3</version>
        </dependency> 
```

添加@JsonIgnore，则忽略这个对象的Json转译。

```
private String name;
    @JsonIgnore
    private Dept dept;	
输出结果：{"name":"jndx","emp":[{"name":"zx"},{"name":"hkw"}]} 
```

**线程诊断**

- cpu占用过多
  - 定位进程，top找出占用cpu过多的进程PID
  - ps -H -eo pid,tip,%cpu | grep PID 找出该进程下占用过大的线程 TID
  - jstack pid 查看进程中各线程的详细信息，可以看出问题所在。注意：这里TID为十六进制，TID转换为16进制。
- 运行迟迟没结果
  - jstack pid 查看进程中各线程的详细信息，拉到最后，发现死锁问题。

![image.png](http://47.98.150.21/upload/2020/3/image-6dc60abfac9c4b9a84b950e4e9986a8d.png)

查看线程详细信息后，发现问题出现在JAVA程序内29和21行。

![image.png](http://47.98.150.21/upload/2020/3/image-88c8b7d68bcb4ac6bae4a60b7c5e0091.png)

分析程序可知，两个线程各自都想锁住两个对象，且开始都各自锁住一个不同的对象，两个线程都无法获得另一个对象，造成死锁。

### 03_本地方法栈(线程私有)

本地方法栈类似于虚拟机栈，也是线程私有。

不同点：本地方法栈服务的对象是jvm运行的native方法，而虚拟机栈服务的是jvm执行的java方法。

### 04_堆(线程共享)

**定义：** Heap，通过new关键字创建的对象，都存放在堆内存中。

**特点**

- 线程共享，堆中的对象都存在线程安全的问题
- 垃圾回收，垃圾回收机制重点区域。

**一个简单的例子** java.lang.OutOfMemeryError:Java Heap space

![image.png](http://47.98.150.21/upload/2020/3/image-841e9445e8de4fce8b4de7e0c4fc8eaa.png)

程序执行过程中，list对象始终被关联，无法被回收，死循环不断将list规模变大，最终大于堆内存大小，内存溢出。

**堆内存诊断**

- jps
  - 查看系统有哪些进程。
- jmap
  - 查看堆内存使用情况 jmap -heap PID
- jconsole
  - 图形界面，多功能检测工具，连续监测。

```
public class Demo1 {
    public static  void main(String[] args) throws InterruptedException {
        System.out.println("1....");
        Thread.sleep(30000);
        byte[] array =  new byte[1024 * 1024 * 10];//10M
        System.out.println("2....");
        Thread.sleep(30000);
        array = null;//array没有引用，可以被回收了
        System.gc();
        System.out.println("3...");
        Thread.sleep(1000000L);
    }
} 
```

**运行上面代码，首先得到Demo进程PID，后每次输出后，jmap -heap PID得到如下内容**

```
D:\openSourceProject\jvm1>jps
8916 Launcher
9876 RemoteMavenServer36
11656
13976 Demo1
13756 Jps

D:\openSourceProject\jvm1>jmap -heap 13976
Attaching to process ID 13976, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.221-b11

using thread-local object allocation.
Parallel GC with 8 thread(s)

Heap Configuration:
   MinHeapFreeRatio         = 0
   MaxHeapFreeRatio         = 100
   MaxHeapSize              = 4276092928 (4078.0MB)
   NewSize                  = 89128960 (85.0MB)
   MaxNewSize               = 1425014784 (1359.0MB)
   OldSize                  = 179306496 (171.0MB)
   NewRatio                 = 2
   SurvivorRatio            = 8
   MetaspaceSize            = 21807104 (20.796875MB)
   CompressedClassSpaceSize = 1073741824 (1024.0MB)
   MaxMetaspaceSize         = 17592186044415 MB
   G1HeapRegionSize         = 0 (0.0MB)

Heap Usage:
PS Young Generation
Eden Space:
   capacity = 67108864 (64.0MB)
   used     = 6711104 (6.40020751953125MB)
   free     = 60397760 (57.59979248046875MB)
   10.000324249267578% used
From Space:
   capacity = 11010048 (10.5MB)
   used     = 0 (0.0MB)
   free     = 11010048 (10.5MB)
   0.0% used
To Space:
   capacity = 11010048 (10.5MB)
   used     = 0 (0.0MB)
   free     = 11010048 (10.5MB)
   0.0% used
PS Old Generation
   capacity = 179306496 (171.0MB)
   used     = 0 (0.0MB)
   free     = 179306496 (171.0MB)
   0.0% used

3175 interned Strings occupying 260400 bytes.

D:\openSourceProject\jvm1>jmap -heap 13976
Attaching to process ID 13976, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.221-b11

using thread-local object allocation.
Parallel GC with 8 thread(s)

Heap Configuration:
   MinHeapFreeRatio         = 0
   MaxHeapFreeRatio         = 100
   MaxHeapSize              = 4276092928 (4078.0MB)
   NewSize                  = 89128960 (85.0MB)
   MaxNewSize               = 1425014784 (1359.0MB)
   OldSize                  = 179306496 (171.0MB)
   NewRatio                 = 2
   SurvivorRatio            = 8
   MetaspaceSize            = 21807104 (20.796875MB)
   CompressedClassSpaceSize = 1073741824 (1024.0MB)
   MaxMetaspaceSize         = 17592186044415 MB
   G1HeapRegionSize         = 0 (0.0MB)

Heap Usage:
PS Young Generation
Eden Space:
   capacity = 67108864 (64.0MB)
   used     = 17196880 (16.400222778320312MB)
   free     = 49911984 (47.59977722167969MB)
   25.62534809112549% used
From Space:
   capacity = 11010048 (10.5MB)
   used     = 0 (0.0MB)
   free     = 11010048 (10.5MB)
   0.0% used
To Space:
   capacity = 11010048 (10.5MB)
   used     = 0 (0.0MB)
   free     = 11010048 (10.5MB)
   0.0% used
PS Old Generation
   capacity = 179306496 (171.0MB)
   used     = 0 (0.0MB)
   free     = 179306496 (171.0MB)
   0.0% used

3176 interned Strings occupying 260456 bytes.

D:\openSourceProject\jvm1>jmap -heap 13976
Attaching to process ID 13976, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.221-b11

using thread-local object allocation.
Parallel GC with 8 thread(s)

Heap Configuration:
   MinHeapFreeRatio         = 0
   MaxHeapFreeRatio         = 100
   MaxHeapSize              = 4276092928 (4078.0MB)
   NewSize                  = 89128960 (85.0MB)
   MaxNewSize               = 1425014784 (1359.0MB)
   OldSize                  = 179306496 (171.0MB)
   NewRatio                 = 2
   SurvivorRatio            = 8
   MetaspaceSize            = 21807104 (20.796875MB)
   CompressedClassSpaceSize = 1073741824 (1024.0MB)
   MaxMetaspaceSize         = 17592186044415 MB
   G1HeapRegionSize         = 0 (0.0MB)

Heap Usage:
PS Young Generation
Eden Space:
   capacity = 67108864 (64.0MB)
   used     = 1342200 (1.2800216674804688MB)
   free     = 65766664 (62.71997833251953MB)
   2.0000338554382324% used
From Space:
   capacity = 11010048 (10.5MB)
   used     = 0 (0.0MB)
   free     = 11010048 (10.5MB)
   0.0% used
To Space:
   capacity = 11010048 (10.5MB)
   used     = 0 (0.0MB)
   free     = 11010048 (10.5MB)
   0.0% used
PS Old Generation
   capacity = 179306496 (171.0MB)
   used     = 1106008 (1.0547714233398438MB)
   free     = 178200488 (169.94522857666016MB)
   0.6168253937659905% used

3162 interned Strings occupying 259464 bytes. 
```

**jconsole使用** 控制台输入jconsole

![image.png](http://47.98.150.21/upload/2020/3/image-50bd0d32512e4654a6e0e307178e54fd.png)可明显看出内存变化。

**一个简单的案例**

- 垃圾回收后，内存占用依然很高
  - 1.控制台输入jvistualvm，在1处选择对应进程，2处点“dump”。
  - 2.点击“查找”，点击第一条占用内存最大的记录。
  - 3.找到问题所在，list中有过多大对象student，无法被清除。

![image.png](http://47.98.150.21/upload/2020/3/image-bc010803acb848ffb0a5bed4e1f7953e.png)
![image.png](http://47.98.150.21/upload/2020/3/image-8b4f802554d04dd1835774d7c468a896.png)
![image.png](http://47.98.150.21/upload/2020/3/image-d18d33cd470d4f589ec2587585a9d353.png)

```
public class Demo2 {
    public static  void main(String[] args) throws InterruptedException {
        List<student> list = new ArrayList<>();
        for (int i = 0; i < 200;i++){
            list.add(new student());
        }
        Thread.sleep(10000000000L);
    }
}

class student{
    private byte[] big = new byte[1024 * 1024];
} 
```

### 05_方法区（线程共享）

**定义：** 其中主要存储class文件的信息和运行时常量池,class文件的信息包括类信息和class文件常量池。

- class文件结构
  - 最头的4个字节用于存储魔数Magic Number，用于确定一个文件是否能被JVM接受
  - 接着4个字节用于存储版本号，前2个字节存储次版本号，后2个存储主版本号
  - 再接着是用于存放常量的常量池，由于常量的数量是不固定的，所以常量池的入口放置一个U2类型的数据(constant_pool_count)存储常量池容量计数值
  - 类信息
  - 父类与接口数组
  - 方法信息

```
 MD5 checksum eea03e744fc6601496ec2c987e07d595
  Compiled from "Stringtable.java"
public class com.example.jvm.Stringtable
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #12.#25        // java/lang/Object."<init>":()V
   #2 = String             #26            // a
   #3 = String             #27            // b
   #4 = String             #28            // ab
   #5 = Class              #29            // java/lang/StringBuilder
   #6 = Methodref          #5.#25         // java/lang/StringBuilder."<init>":()V
   #7 = Methodref          #5.#30         // java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
   #8 = Methodref          #5.#31         // java/lang/StringBuilder.toString:()Ljava/lang/String;
   #9 = Fieldref           #32.#33        // java/lang/System.out:Ljava/io/PrintStream;
  #10 = Methodref          #34.#35        // java/io/PrintStream.println:(Z)V
  #11 = Class              #36            // com/example/jvm/Stringtable
  #12 = Class              #37            // java/lang/Object
  #13 = Utf8               <init>
  #14 = Utf8               ()V
  #15 = Utf8               Code
  #16 = Utf8               LineNumberTable
  #17 = Utf8               main
  #18 = Utf8               ([Ljava/lang/String;)V
  #19 = Utf8               StackMapTable
  #20 = Class              #38            // "[Ljava/lang/String;"
  #21 = Class              #39            // java/lang/String
  #22 = Class              #40            // java/io/PrintStream
  #23 = Utf8               SourceFile
  #24 = Utf8               Stringtable.java
  #25 = NameAndType        #13:#14        // "<init>":()V
  #26 = Utf8               a
  #27 = Utf8               b
  #28 = Utf8               ab
  #29 = Utf8               java/lang/StringBuilder
  #30 = NameAndType        #41:#42        // append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
  #31 = NameAndType        #43:#44        // toString:()Ljava/lang/String;
  #32 = Class              #45            // java/lang/System
  #33 = NameAndType        #46:#47        // out:Ljava/io/PrintStream;
  #34 = Class              #40            // java/io/PrintStream
  #35 = NameAndType        #48:#49        // println:(Z)V
  #36 = Utf8               com/example/jvm/Stringtable
  #37 = Utf8               java/lang/Object
  #38 = Utf8               [Ljava/lang/String;
  #39 = Utf8               java/lang/String
  #40 = Utf8               java/io/PrintStream
  #41 = Utf8               append
  #42 = Utf8               (Ljava/lang/String;)Ljava/lang/StringBuilder;
  #43 = Utf8               toString
  #44 = Utf8               ()Ljava/lang/String;
  #45 = Utf8               java/lang/System
  #46 = Utf8               out
  #47 = Utf8               Ljava/io/PrintStream;
  #48 = Utf8               println
  #49 = Utf8               (Z)V
{
  public com.example.jvm.Stringtable();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 3: 0

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=3, locals=5, args_size=1
         0: ldc           #2                  // String a
         2: astore_1
         3: ldc           #3                  // String b
         5: astore_2
         6: ldc           #4                  // String ab
         8: astore_3
         9: new           #5                  // class java/lang/StringBuilder
        12: dup
        13: invokespecial #6                  // Method java/lang/StringBuilder."<init>":()V
        16: aload_1
        17: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
        20: aload_2
        21: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
        24: invokevirtual #8                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
        27: astore        4
        29: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
        32: aload         4
        34: aload_3
        35: if_acmpne     42
        38: iconst_1
        39: goto          43
        42: iconst_0
        43: invokevirtual #10                 // Method java/io/PrintStream.println:(Z)V
        46: return
      LineNumberTable:
        line 6: 0
        line 7: 3
        line 8: 6
        line 9: 9
        line 10: 29
        line 11: 46
      StackMapTable: number_of_entries = 2
        frame_type = 255 /* full_frame */
          offset_delta = 42
          locals = [ class "[Ljava/lang/String;", class java/lang/String, class java/lang/String, class java/lang/String, class java/l
ang/String ]
          stack = [ class java/io/PrintStream ]
        frame_type = 255 /* full_frame */
          offset_delta = 0
          locals = [ class "[Ljava/lang/String;", class java/lang/String, class java/lang/String, class java/lang/String, class java/l
ang/String ]
          stack = [ class java/io/PrintStream, int ]
} 
```

**组成与实现：** jdk6（永久代实现）和jdk8（元空间实现）中方法区的区别，其中最主要的区别是8中将方法区转移到本地内存中，且常量池分为运行时常量池和字符串常量池；且字符串常量池被留在内存中的堆中。

![image](http://47.98.150.21/upload/2020/3/image-0402a2e372aa4bce8c0fd8e3089c3eea.png)

**内存溢出**

- 永久代jdk1.6

![image.png](http://47.98.150.21/upload/2020/3/image-f92607f6a685449aa0445c2050bf61fb.png)

- 元空间jdk1.8

![image.png](http://47.98.150.21/upload/2020/3/image-73eda07d6ab24be28df5736c8749b035.png)

**场景**

- spring
- mybatis 都会产生大量的类，从而造成方法区内存溢出。

**了解常量池，运行时常量池，字符串池**

- 全局字符串池（string pool也有叫做string literal pool）。
  - 全局字符串池里的内容是在类加载完成，经过验证，准备阶段之后在堆中生成字符串对象实例，然后将该字符串对象实例的引用值存到string pool中（记住：string pool中存的是引用值而不是具体的实例对象，具体的实例对象是在堆中开辟的一块空间存放的。）。
  - 在HotSpot VM里实现的string pool功能的是一个StringTable类，它是一个哈希表，里面存的是驻留字符串(也就是我们常说的用双引号括起来的)的引用（而不是驻留字符串实例本身），也就是说在堆中的某些字符串实例被这个StringTable引用之后就等同被赋予了”驻留字符串”的身份。这个StringTable在每个HotSpot VM的实例只有一份，被所有的类共享。
- class文件常量池（class constant pool）：class文件中除了包含类的版本、字段、方法、接口等描述信息外，还有一项信息就是常量池(constant pool table)，用于存放编译器生成的各种字面量(Literal)和符号引用(Symbolic References)。
  - 字面量就是我们所说的常量概念，如文本字符串、被声明为final的常量值等。
  - 符号引用是一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能无歧义地定位到目标即可。
    - 类和接口的全限定名
    - 字段名称和描述符
    - 方法名称和描述符
- 运行时常量池：
  - jvm在执行某个类的时候，必须经过加载、连接、初始化，而连接又包括验证、准备、解析三个阶段。
  - 当类加载到内存中后，jvm就会将class常量池中的内容存放到运行时常量池中，由此可知，运行时常量池也是每个类都有一个。
  - class常量池中存的是字面量和符号引用，也就是说他们存的并不是对象的实例，而是对象的符号引用值。而经过解析（resolve）之后，也就是把符号引用替换为直接引用，解析的过程会去查询全局字符串池，也就是我们上面所说的StringTable，以保证运行时常量池所引用的字符串与全局字符串池中所引用的是一致的。

**举例说明**

```
public class HelloWorld {
    public static void main(String []args) {
		String str1 = "abc"; 
		String str2 = new String("def"); 
		String str3 = "abc"; 
		String str4 = str2.intern(); 
		String str5 = "def"; 
		System.out.println(str1 == str3);//true 
		System.out.println(str2 == str4);//false 
		System.out.println(str4 == str5);//true
    }
} 
```

- String str1 = "abc";
  - 在堆中会有一个”abc”实例，全局StringTable中存放着”abc”的一个引用值
- String str2 = new String("def"); 会生成两个实例。
  - 一个是”def”的实例对象，并且StringTable中存储一个”def”的引用值
  - 还有一个是new出来的一个”def”的实例对象，与上面那个是不同的实例
- String str3 = "abc"; 解析str3时，在StringTable中寻找“abc”，发现有全局字符引用，所以str3的引用地址和str1相同
- String str4 = str2.intern();
  - intern()函数返回StringTable中”def”的引用值。因为StringTable中已经有“def”引用值。
  - 返回str2中new出来的“def”在StringTable中的引用值。
  - str5亦是如此。

**所以输出的布尔值就可以理解了**

**总结**

1. 全局字符串池每个虚拟机只有一个，存储字符串常量的引用值；
2. class常量池是java程序编译之后才有的，每个类都有，存放字面值和符号引用常量；
3. 运行时常量池是在类加载完之后，常量池内容存储在运行时常量池中，每个类都有一个，且常量池中符号引用转换为直接引用，与全局字符串池中保持一致。

**全局字符串池必知必会**

1. 编译期优化

```
String s1 = "a";

        String s2 = "b";
        String s3 = "ab";
        /*
            new StringBuilder().append("a").append("b").toString();
             s1 s2 为变量，其值没有确定
         */
        String s4 = s1+s2;
        System.out.println(s4==s3);//false
         /*
            javac编译期优化，“a”，“b”已经确定，所以在编译期s5就已经确定为“ab”
            在串池中寻找到“ab”，返回“ab”引用值
         */
        String s5 = "a"+"b";
        System.out.println(s3==s5);//true 
```

1. 字符串延迟加载

```
 String s1 = "a";
	//java.lang.string.count = 2361
        String s2 = "b";
	//java.lang.string.count = 2362
        String s3 = "ab";
	//java.lang.string.count = 2363 
```

1. 常量池和串池的关系
   - 常量池中的都会被加载到运行时常量池中，此时字符串都是常量池中的符号，尚且未转化为对象
   - 运行时，将String字面量符号“a”转化为字符串对象，并在StringTable中寻找“a”，如多没有则添加“a”字符串对象。
   - StringTable 结构为HashTable，且不能扩容。
2. intern方法的运用jdk1.8

```
public class internDemo {
    public static void main(String[] args) {
        //new String("a") new String("b") new String("ab")
        //StringTable["a","b"] "ab"属于动态拼接，未加入StringTable
        String s = new String("a") + new String("b");

        //使用intern方法 在StringTable中放入此字符串对象，如果有则返回给字符串对象，没有在放入
        String s2 = s.intern();

        //s2指向的是串池中字符串对象 s也被放入串池中
        System.out.println(s==s2);//true
    }
}



public class internDemo {
    public static void main(String[] args) {
        String s3 = "ab";

        //new String("a") new String("b") new String("ab")
        //StringTable["a","b","ab"]

        String s = new String("a") + new String("b");
        //使用intern方法 在StringTable中放入此字符串对象，如果有则返回给字符串对象，没有在放入

        String s2 = s.intern();
        //s2指向的是串池中字符串对象 s未被放入串池，还在堆中

        System.out.println(s3==s2);//true
        System.out.println(s3==s);//false

    }
} 
```

1. intern方法的运用jdk1.6

```
public class internDemo {
    public static void main(String[] args) {
       
        //new String("a") new String("b") new String("ab")
        //StringTable["a","b"]
        String s = new String("a") + new String("b");
        //使用intern方法 在StringTable中放入此字符串对象，如果有则返回给字符串对象，没有则++复制一份后放入++
        String s2 = s.intern();
        //s2指向的是串池中字符串对象(s复制后的对象) ,s未被放入串池，还在堆中
	//StringTable["a","b","ab"]
 	String s3 = "ab";
        System.out.println(s3==s2);//true
        System.out.println(s3==s);//true

    }
} 
```

1. StringTable位置
   - jdk1.6及之前，StringTable与方法区一同在永久代中。
   - jdk1.8之后，方法区转移到本地内存中，但是将StringTable转移到堆内存中。
   - 原因
     - StringTable中存在大量的字符串对象，运行时间增长永久代内存占用过多，且永久代只有在触发FULL GC时才进行垃圾回收，回收频率过慢。
     - 转移到堆中可以利用虚拟机在堆内存中频繁的垃圾回收，处理StringTable中对象过多情况。
2. StringTable垃圾回收

```
package com.example.jvm;
//-Xmx10m  -XX:+PrintStringTableStatistics -XX:+PrintGCDetails -verbose:gc
public class stringTableGc {
    public static void main(String[] args) throws  InterruptedException{
        int i = 0;
        try {
            for (int j = 0;j< 10000; j++){
                String.valueOf(j).intern();
            }
        }
        catch (Exception e){
            e.printStackTrace();
        }
        finally {
            System.out.println();
        }
    }

}


[GC (Allocation Failure) [PSYoungGen: 2048K->488K(2560K)] 2048K->777K(9728K), 0.0305935 secs] [Times: user=0.00 sys=0.00, real=0.03 secs] 

Heap
 PSYoungGen      total 2560K, used 815K [0x00000000ffd00000, 0x0000000100000000, 0x0000000100000000)
  eden space 2048K, 16% used [0x00000000ffd00000,0x00000000ffd51fa0,0x00000000fff00000)
  from space 512K, 95% used [0x00000000fff00000,0x00000000fff7a020,0x00000000fff80000)
  to   space 512K, 0% used [0x00000000fff80000,0x00000000fff80000,0x0000000100000000)
 ParOldGen       total 7168K, used 289K [0x00000000ff600000, 0x00000000ffd00000, 0x00000000ffd00000)
  object space 7168K, 4% used [0x00000000ff600000,0x00000000ff6486c0,0x00000000ffd00000)
 Metaspace       used 3083K, capacity 4496K, committed 4864K, reserved 1056768K
  class space    used 336K, capacity 388K, committed 512K, reserved 1048576K
SymbolTable statistics:
Number of buckets       :     20011 =    160088 bytes, avg   8.000
Number of entries       :     12922 =    310128 bytes, avg  24.000
Number of literals      :     12922 =    555888 bytes, avg  43.019
Total footprint         :           =   1026104 bytes
Average bucket size     :     0.646
Variance of bucket size :     0.645
Std. dev. of bucket size:     0.803
Maximum bucket size     :         6
StringTable statistics:
Number of buckets       :     60013 =    480104 bytes, avg   8.000
Number of entries       :      6426 =    154224 bytes, avg  24.000
Number of literals      :      6426 =    381360 bytes, avg  59.346
Total footprint         :           =   1015688 bytes
Average bucket size     :     0.107
Variance of bucket size :     0.107
Std. dev. of bucket size:     0.326
Maximum bucket size     :         3

Process finished with exit code 0 
```

1. StringTable调优
   - 调整hash表中桶子个数，-XX:StringTableSize=桶个数
   - 考虑字符串是否入池

### 06_直接内存

- 常见于NIO操作中，用于数据缓冲
- 分配回收成本高，但读写能力强
- 不受JVM内存回收管理

**直接内存使用前和使用后**

1. 使用前
   ![image.png](http://47.98.150.21/upload/2020/3/image-8c8a4aab90da4c5bb73d57170dc0cb44.png)

- 因为java无法操作本地文件，在java堆内存中划出java缓冲区；
- 从用户态转移到内核态，本地方法在系统内存中划出一段系统缓冲区，将磁盘文件分部分缓冲到系统缓冲区中，间接的将系统缓冲区中数据传输到java缓冲区中；
- 内核态转到用户态，调用输出流写入操作，将文件copy到另一个位置，循环copy，直到全部复制完成。

1. 使用后

![image.png](http://47.98.150.21/upload/2020/3/image-c564190a066b4cb287d902cf90a79439.png)

- ByteBuffer.allocateDirect(_size)，在系统内存中分配直接内存；
- 系统方法和java方法都可以访问直接内存；
- 与不使用直接内存相比，减少了一次从系统缓存区向java缓冲区复制的操作，复制效率成倍上升。

**直接内存溢出**

```
package com.example.jvm;

import java.nio.ByteBuffer;
import java.util.ArrayList;
import java.util.List;

public class Demo3 {

    public static void main(String[] args) {
        List<ByteBuffer> list = new ArrayList<>();
        int i = 0;
        try {
            while(true){
                ByteBuffer byteBuffer = ByteBuffer.allocateDirect(100*1024*1024);
                list.add(byteBuffer);
                i++;
            }
        }finally {
            System.out.println(i);
        }
    }
}

36  3.6G
Exception in thread "main" java.lang.OutOfMemoryError: Direct buffer memory
	at java.nio.Bits.reserveMemory(Bits.java:694)
	at java.nio.DirectByteBuffer.<init>(DirectByteBuffer.java:123)
	at java.nio.ByteBuffer.allocateDirect(ByteBuffer.java:311)
	at com.example.jvm.Demo3.main(Demo3.java:14) 
```

**分配和回收原理**

- 使用Unsafe对象实现直接内存的分配回收，回收主要使用的是freeMemory方法
- ByteBuffer类内部，使用了Cleaner（虚引用）来检测ByteBuffer对象，一旦对象被回收，就会由ReferenceHandler线程通过Cleaner的clean对象调用freeMenory来释放直接内存。
- -XX:+DisableExplicitGC 显式的System.gc()显式的垃圾回收 FULL GC，被禁用。
- 因为考虑到系统性能，FULL GC时间够长，会严重影响性能。所以涉及到直接内存的使用，释放内存使用Unsafe.freeMemory，不建议使用System.gc()。

## 3 垃圾回收

### 01_判断对象是否可以回收

1. 引用计数法
   - 如果两个对象互相引用，计数器都为1，即使他们都没有被使用，都不会被清理。
2. 可达性分析算法
   - Java虚拟机中的垃圾回收器采用可达性分析来探索所有的对象
   - 扫描堆中的对象，判断是否能根据GC Root的引用链找到该对象，找不到则回收。
3. 五种引用
   - 强引用 软引用 弱引用 虚引用 终结器引用

**哪些对象可以作为GC Root？**
\1. 使用Eclipse开发的一款分析内存的软件MAT.[MAT](https://www.eclipse.org/mat/downloads.php)
\2. jmap -dump:format=b,live,file=2.bin 线程ID ；
\3. mat打开2.bin（文件名）
**举例说明**

```
package com.example.jvm;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class rootSearch {
    public static void main(String[] args) throws  InterruptedException , IOException {
        List<String> list = new ArrayList<>();
        list.add("a");
        list.add("a");
        System.out.println(1);
	//jmap -dump:format=b,live,file=1.bin 12436
        System.in.read();

        list = null;
        System.out.println(2);
//jmap -dump:format=b,live,file=2.bin 12436
        System.in.read();

        System.out.println("end结束。。。。");

    }
} 
```

![image.png](http://47.98.150.21/upload/2020/3/image-15200c26f9cf443091a0fba3c3783ff8.png)

![image.png](http://47.98.150.21/upload/2020/3/image-6b4fb4417bad4307848b4cb3576dfd44.png)

在list=null后，GC Root中list对象被回收。

**引用关系回收判断**

1. 强引用，指向某一对象的所有强引用都断开，该对象才能被回收。
2. 软、弱引用，指向某一被软、弱引用的对象的所有强引用都断开，该对象可能被回收。
   - 软引用：如果垃圾回收之后，内存依然不足，只被软引用的对象会被回收。
   - 弱引用：只要发生垃圾回收，只被弱引用的对象就会被回收。
   - 对象回收后，软弱引用本身转移到引用队列中。
   - 遍历引用队列，释放引用。
3. 虚引用
   - 虚引用的ByteBuffer，没有被强引用，被回收掉，分配的直接内存尚未回收
   - 虚引用进入引用队列中，RefferenceHandler在队列中寻找到虚引用Cleaner
   - 调用Unsafe.freeMemory()方法释放直接内存；
   - 释放引用。
4. 终结器引用
   - 终结器对象引用的对象没有被强引用，在被回收前，终结器引用转移到引用队列，一个优先级较低的线程finallize在引用队列中寻找终结器引用；
   - 并找到终结器引用的对象，调用finalize()方法；
   - 下次垃圾回收时，回收该对象。
     ![image.png](http://47.98.150.21/upload/2020/3/image-7146eef96f5f4e8d951b13c8ff2fa0a3.png)

![image.png](http://47.98.150.21/upload/2020/3/image-8f432937797d4d4fa1944c563a9440c9.png)

![image.png](http://47.98.150.21/upload/2020/3/image-4be5eeec224c42509bc3afd24da00e4d.png)

![image.png](http://47.98.150.21/upload/2020/3/image-04ac2cf3472d41adbc3a399619e768a3.png)

![image.png](http://47.98.150.21/upload/2020/3/image-fe59b326e32d47f6be8c046473fa1eee.png)

**五种引用举例**

1. 强引用：一般平常代码中大部分引用都是强引用。

```
-Xmx20m
 	List<byte[]> list1 = new ArrayList<>();
        for (int i = 0;i < 5;i++){
            list1.add(new byte[_4MB]);
        }

+++++++++++++打印结果++++++++++++++
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	at com.example.jvm.GC.softY.main(softY.java:12)

++++++++++++++++解释+++++++++++++++++++
-Xmx20m将堆内存最大设置为20m，强引用byte数组无法被回收，25m>20m，所以堆内存溢出。 
```

1. 软引用

```
-Xmx20m -XX:+PrintGCDetails -verbose:gc

public static  void soft(){
        List<SoftReference<byte[]>> list = new ArrayList<>();
        for (int i = 0;i < 5;i++){
            SoftReference<byte[]> ref = new SoftReference<>(new byte[_4MB]);
            System.out.println(ref.get());
            list.add(ref);
            System.out.println(list.size());
        }
        System.out.println("循环结束,"+ list.size());
        for (SoftReference<byte[]> ref:list){
            System.out.println(ref.get());
        }
    }

++++++打印结果++++++
[B@3fee733d
1
[B@5acf9800
2
[B@4617c264
3
++++++++++
[GC (Allocation Failure) [PSYoungGen: 1953K->496K(6144K)] 14241K->13133K(19968K), 0.0013159 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@36baf30c
4
[GC (Allocation Failure) --[PSYoungGen: 4817K->4817K(6144K)] 17455K->17455K(19968K), 0.0008319 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Ergonomics) [PSYoungGen: 4817K->4488K(6144K)] 
[ParOldGen: 12637K->12550K(13824K)] 17455K->17038K(19968K),
 [Metaspace: 3085K->3085K(1056768K)], 0.0054989 secs]
 [Times: user=0.00 sys=0.00, real=0.01 secs] 
[GC (Allocation Failure) --[PSYoungGen: 4488K->4488K(6144K)] 
17038K->17038K(19968K), 0.0009131 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Allocation Failure) [PSYoungGen: 4488K->0K(6144K)] 
[ParOldGen: 12550K->637K(8704K)] 17038K->637K(14848K), 
[Metaspace: 3085K->3085K(1056768K)], 0.0076801 secs]
 [Times: user=0.02 sys=0.00, real=0.01 secs] 
[B@7a81197d
5
++++++++++++++++++++++
GC日志可以看出，Eden区内存不足触发的 minor GC只是将新生代对象转移到老年代，并没有回收。
在老年代内存不足时，触发FULL GC ，但是第一次触发并未回收，直到第二次触发再回收所有软引用对象。
++++++++++++++++
循环结束,5
null
null
null
null
[B@7a81197d
Heap
 PSYoungGen      total 6144K, used 4433K [0x00000000ff980000, 0x0000000100000000, 0x0000000100000000)
  eden space 5632K, 78% used [0x00000000ff980000,0x00000000ffdd4500,0x00000000fff00000)
  from space 512K, 0% used [0x00000000fff00000,0x00000000fff00000,0x00000000fff80000)
  to   space 512K, 0% used [0x00000000fff80000,0x00000000fff80000,0x0000000100000000)
 ParOldGen       total 8704K, used 637K [0x00000000fec00000, 0x00000000ff480000, 0x00000000ff980000)
  object space 8704K, 7% used [0x00000000fec00000,0x00000000fec9f660,0x00000000ff480000)
 Metaspace       used 3130K, capacity 4500K, committed 4864K, reserved 1056768K
  class space    used 341K, capacity 388K, committed 512K, reserved 1048576K 
```

**软引用配合引用队列**
上述为配合引用队列的做法，虽然对象被回收，但是软引用没有被回收，因为最后输出了四个NULL，软引用还在。

```
 public static  void soft(){
        List<SoftReference<byte[]>> list = new ArrayList<>();
        //引用队列
        ReferenceQueue<byte[]> queue = new ReferenceQueue<>();

        for (int i = 0;i < 5;i++){
            SoftReference<byte[]> ref = new SoftReference<>(new byte[_4MB],queue);
            System.out.println(ref.get());
            list.add(ref);
            System.out.println(list.size());
        }
        Reference<? extends byte[]> poll = queue.poll();
        while(poll !=null){
            list.remove(poll);
            poll = queue.poll();
        }
        System.out.println("循环结束,"+ list.size());
        for (SoftReference<byte[]> ref:list){
            System.out.println(ref.get());
        }
    }
++++运行结果++++
1
[B@5acf9800
2
[B@4617c264
3
[B@36baf30c
4
[B@7a81197d
5
循环结束,1
[B@7a81197d
++++++软引用被清除 NULL消失++++++ 
```

1. 弱引用

```
 public static  void weak(){
        List<WeakReference<byte[]>> list = new ArrayList<>();
        for (int i = 0;i < 10;i++){
            WeakReference<byte[]> ref = new WeakReference<>(new byte[_4MB]);
            list.add(ref);
            for (WeakReference<byte[]> ref1:list){
                System.out.print(ref1.get()+" ");
            }
            System.out.println();
        }
    }
++++打印结果+++
[B@3fee733d 
[B@3fee733d [B@5acf9800 
[B@3fee733d [B@5acf9800 [B@4617c264 
[GC (Allocation Failure) [PSYoungGen: 1953K->488K(6144K)] 14241K->13069K(19968K), 0.0006807 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@3fee733d [B@5acf9800 [B@4617c264 [B@36baf30c 
[GC (Allocation Failure) [PSYoungGen: 4809K->488K(6144K)] 17391K->13085K(19968K), 0.0005917 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@3fee733d [B@5acf9800 [B@4617c264 null [B@7a81197d 
[GC (Allocation Failure) [PSYoungGen: 4751K->488K(6144K)] 17349K->13125K(19968K), 0.0004115 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@3fee733d [B@5acf9800 [B@4617c264 null null [B@5ca881b5 
[GC (Allocation Failure) [PSYoungGen: 4731K->488K(6144K)] 17369K->13133K(19968K), 0.0004320 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@3fee733d [B@5acf9800 [B@4617c264 null null null [B@24d46ca6 
[GC (Allocation Failure) [PSYoungGen: 4718K->504K(6144K)] 17363K->13157K(19968K), 0.0003284 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@3fee733d [B@5acf9800 [B@4617c264 null null null null [B@4517d9a3 
[GC (Allocation Failure) [PSYoungGen: 4725K->488K(5120K)] 17379K->13173K(18944K), 0.0003207 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@3fee733d [B@5acf9800 [B@4617c264 null null null null null [B@372f7a8d 
[GC (Allocation Failure) [PSYoungGen: 4690K->160K(5632K)] 17376K->13276K(19456K), 0.0003747 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Ergonomics) [PSYoungGen: 160K->0K(5632K)] [ParOldGen: 13116K->714K(8192K)] 13276K->714K(13824K), [Metaspace: 3159K->3159K(1056768K)], 0.0045601 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
null null null null null null null null null [B@2f92e0f4 

+++++++++++++++结论++++++++++++++++
弱引用对象在首次触发minor GC时，将弱引用对象转移到老年代中，再次触发 minor GC时，回收在Eden中的新生对象。
因为软引用占用内存，在没有及时回收引用时，老年代内存逐渐被软引用占用，直到内存不足，触发FULL GC ，回收所有的弱引用对象。
+++++++++++++++问题++++++++++++++++++
这样弱引用未被回收，还是会一直占用内存，所以还是需要配合引用队列回收弱引用，具体操作参考软引用。 
```

\### 02_回收算法 1. 标记清除 - 速度较快 - 会造成内存碎片化 ![image.png](http://47.98.150.21/upload/2020/3/image-467ea48c827947d99130701448d2af0d.png)

1. 标记整理
   - 速度慢
   - 不会有内存碎片
     ![image.png](http://47.98.150.21/upload/2020/3/image-3cf83bb2c39341e4b997a81facb45480.png)
2. 复制
   - 不会有内存碎片
   - 需要占用双倍内存空间

![image.png](http://47.98.150.21/upload/2020/3/image-e20d4fcfc76048c8a17a2648213560f7.png)
**总结**

- 复制算法回收速度与存活对象数量相关，数量越少，速度越快，如果存活对象过多，速度可能会比标记整理速度要慢。
- 实际JVM垃圾回收是根据情况配合三种算法使用，因为三种算法都有其实用的场景。

### 03_分代回收

![image.png](http://47.98.150.21/upload/2020/3/image-7733e793f8594b83a13f9d9568cfa4e3.png)

1. 对象首先分配在Eden区内；
2. 在新生代空间不足时，触发*Minor GC*，将*Eden*和*From*中存活的对象复制到*To*中，存活的对象年龄加1，并且交换*To*和*From*；
   - *Minor GC*触发 *STOP THE WORLD(STW)*，暂停其他用户线程，直到垃圾回收完毕，才回复线程运行。
   - 当对象寿命超过阈值时，会晋升至老年代，默认阈值为15。
   - 设置-XXPretenureSizeThreshold=3145728（3M）大对象会直接进入老年代，
3. 当老年代空间不足，尝试*Minor GC*，空间仍不足，触发*Full GC*，触发 *STW*，时间相比于*Minor GC*会长的多，因为新生代和老年代回收算法不同，且老年代中对象多，回收较为复杂。

#### 03_1相关VM参数

![image.png](http://47.98.150.21/upload/2020/3/image-c62a2caab83e44b58b26eaa36715d888.png)

### 04_垃圾收集器

- 串行
  - 单线程
  - 堆内存较小，适用于个人电脑
- 吞吐量优先
  - 多线程
  - 堆内存大，多核cpu
  - 单位时间内STW时间最小 0.2+0.2 = 0.4
- 响应时间优先
  - 多线程
  - 堆内存大，多核cpu
  - 尽可能STW单次时间减小 0.1+0.1+0.1+0.1+0.1=0.5

#### 04_1串行垃圾回收器

![image.png](http://47.98.150.21/upload/2020/3/image-0369a8ef96fc42e58a44199b034dcb06.png)
其中新生代使用Serial，老年代使用SerialOld垃圾回收器。

- *Serial*垃圾回收器使用的是复制算法，新生代中对象变化很大，存活对象相对于其他分区比较少，使用复制算法能够有效地提高效率。
- *SerialOld*使用的是标记整理算法，老年代存活的都是些价值很高的对象，回收的对象数量很少，整理耗用很小，复制存活对象会消耗更多的时间，且会耗用多一倍的内存空间(老年代本身空间就已经较大)。

#### 04_2吞吐量优先回收器

![image.png](http://47.98.150.21/upload/2020/3/image-6e7f12514cbc424f8a45fcc53a6bf658.png)

- MaxGCPauseMillis设置回收停顿的最大时间。收集器尽可能将垃圾回收消耗时间保持在这个数值之下；但是并不是把这个数设置越小，垃圾收集就越快。
  - 停顿时间减少是以牺牲吞吐量和新生代空间换来的，系统会将新生代内存降低，来达到每次回收停顿时间缩小，但是这也导致了停顿次数增加，总停顿时间并没有减少，吞吐量减小。
- GCTimeRatio设置一个1-99的整数，*1/(1+X)*表示垃圾回收时间占总时间的比率，整数设置越大，吞吐量越大。
  - 这又回到上面那个问题，整数设置越大相当于停顿时间减少，系统会减少新生代内存，导致停顿增加，吞吐量减小。
- 所以这两个参数不可一味的增加某一个，他们互相制约，寻找一个平衡。
- -XX:+UseAdaptiveSizePolicy 系统会根据系统运行情况修改新生代大小(-Xmn),Eden和Survivor比例(-XX:SurvivorRatio)，晋升老年代对象年龄(-XX:PretenureSizeThreshold)等参数；来提供最合适的停顿时间和最大吞吐量。

**总结**
对于不是很了解收集器运作的开发人员来说，*ParallelGC*是个更好的选择。事先设置好堆大小-Xmx，然后设置 *MaxGCPauseMillis*（重视停顿时间）或者*GCTimeRatio*（重视吞吐量）；设置完优化目标，剩下的具体参数修改交给系统自己。
这也是*ParallelGC*和*ParNew*一个重要的区别。

#### 04_3响应时间优先垃圾收集器

![image.png](http://47.98.150.21/upload/2020/3/image-fe161ba291cf48199fa071741878d1e6.png)

- 老年代使用*CMS*垃圾回收器，使用的是标记清除算法。
- 新生代*ParNewGC*垃圾收集器，复制算法(*ParNewGC*是多线程版本的*Serial*)。
- -XX:ParallelGCThread=n，并行线程数，一般为cpu核心数，图中N=4。
- -XX:ConcGCThreads=threads，一般设置为n/4，并发垃圾回收线程数。
- -XX:CMSInitiatingOccupancyFraction=percent，达到比例就会出发老年代垃圾回收。
  - jdk1.6将这一值从1.5中的68改为92，为了防止频繁的触发老年代垃圾回收；
  - 为什么要设置这一比例？
    - 因为在并发垃圾清理的同时，其它用户线程也在运行，产生的浮动垃圾*CMS*当前批次中难以处理，只能留到下次GC；也正是因为如此，所以需要留下空间给用户线程运行所产生的对象使用。
  - 如何设置最好？
    - 在内存无法满足运行需要时，会出现*Concurrent Mode Failure*失败，VM会使用*Serial Old*代替*CMS*对老年代中垃圾对象进行整理，停顿时间更长，比例设置过高，更加容易触发*Concurrent Mode Failure*失败，影响性能（*Serial Old*暂停所有用户线程防止产生更多的对象，同时可以清理内存碎片）。
    - 所以项目产生对象不是很快可以将这一比例设置高一点，这样既不会很快出现*Concurrent Mode Failure*失败，也不会太容易触发GC。
  - 因为*CMS*老年代使标记-清除算法，会产生很多碎片内存，会给大对象分配内存带来麻烦，则会出现老年代内存很充裕，但是大对象放不进去，不得不触发FULL GC；-XX:+UseCMSCompactAtFullCollection默认开启，在CMS要开始FULL GC时，开启内存碎片的合并，无法并发，整理时间变长。
  - -XX:CMSFullGCsBeforeCompaction，设置执行多少次不压缩的FULL GC后，执行一次带压缩的FULL GC。

#### 04_4 G1垃圾收集器

**定义：** Garbage First,jdk9后默认垃圾收集器。

**适用场景**

- 同时注重吞吐量和低延迟，默认暂停目标时200ms。
- 超大堆内存会将内存划分为多个相等的Region。
- 整体上使用标记-整理算法，两个区域使用复制算法。

**相关VM参数**

- -XX:UseG1GC
- -XX:G1HeapRegionSize=size
- -XX:MaxGCPauseMillis=time

##### 04_4_1 G1垃圾回收阶段

![image.png](http://47.98.150.21/upload/2020/3/image-4934882cb0594ea49d7407a9c6aaef54.png)

##### 04_4_2 Young Collection

![image.png](http://47.98.150.21/upload/2020/3/image-b6232f40e5f34b01b6d32f80704d9e8f.png)

如图所示：

- 内存被分为很多个大小相等的区域。
- *E(Eden)\*区内存不足时触发\*minor GC* 将存活对象转移到*S(Surviror)\*区（To），\*From\*中存活对象转移到\*To*中(S->S)，*From*和*To*调换位置(*minor GC* 后*Surviror*中对象存在于*From*中)。
- *S->O*,幸存区对象晋升到老年代。

**跨代引用**

![image.png](http://47.98.150.21/upload/2020/3/image-d5aaeee01f3a4295a55c2fac81d08ec4.png)

- 老年代对象引用了新生代对象，跨代引用。
- 老年代中红色区域为卡表中的脏卡，新生代对象通过Remmember Set记录对应脏卡。
- 在引用变更时，post-write barrier更新脏卡指令放入dirty card queue队列中，等待线程执行。
- current refinement threads 更新 Remmember Set。

##### 04_4_3 Young Collection +CM

![image.png](http://47.98.150.21/upload/2020/3/image-fa37e99c763a49319e60166be9d56c50.png)

- 老年代区域占比达到45%时，触发并发标记。

##### 04_4_4 MixedCollection

![image.png](http://47.98.150.21/upload/2020/3/image-f3055121ecc24ff79ff3e0bb601921ee.png)

**注意**

- *O->O*，回收老年代中的垃圾使用复制算法，将一个老年代区域中存活的对象复制到另一个老年代区域。
- G1垃圾收集器选择回收价值高的老年代区域(即可回收较大的空间)，为了在MaxGCPauseMillis时间内完成垃圾回收。
- 最终标记，需要STW，避免其他用户线程在标记过程中，产生浮动垃圾。

##### 04_4_5 Full GC

- SerialGC
  - 新生代内存不足*minor GC*
  - 老年代内存不足*Full GC*
- ParallelGC
  - 新生代内存不足*minor GC*
  - 老年代内存不足*Full GC*
- CMS
  - 新生代内存不足*minor GC*
  - 老年代内存不足
    - 并发失败会触发FULL GC
- G1
  - 新生代内存不足*minor GC*
  - 老年代内存不足
    - 老年代占用达到阈值时，若垃圾回收大于垃圾产生速度，继续并发回收
    - 反之，会发生FULL GC

##### 04_4_6 Remark

![image.png](http://47.98.150.21/upload/2020/3/image-c2f3f4201e094015adbac5a50ae0a53f.png)

- 图中黑色表示已经处理完毕，灰色表示正在处理，白色表示还未处理。
- 灰色引用白色对象，由于是并发收集，所以该对象存在被其他线程取消引用的可能。
- 当然也有可能将本来已经被回收的没有被引用的白色对象重新被引用，但是对象已经被回收，无法再引用。
- 采用per-write barrior 在引用关系未改变之前设置屏障并放入satb_mark_queue队列中，直到收集完毕。

##### 04_4_7 JDK 8u20 字符串去重

- 优点：节约大量内存
- 缺点：略微多占用cpu运行时间，新生代回收时间略微增加

-XX:+UseStringDEduplication

```
String s1 = new String("hello");//char[]{'h','e','l','l','o'}
String s1 = new String("hello");//char[]{'h','e','l','l','o'} 
```

- 将所有新分配的字符串放入一个队列
- 当新生代回收时，检查是否有重复的字符串
- 相同的字符串指向同样的字符数组char[]
- **注意** 与String.intern（）不同
  - String.intern（）注重的时字符串对象
  - 字符串去重更加注重char[]
  - JVM内部使用不同的字符串去重

##### 04_4_8 JDK 8u40 并发标记类卸载

所有对象在并发标记后，直到哪些类不在被使用，当一个类加载器内所有的类都不再被使用，则卸载他加载的所有类。
-XX:+ClassUnloadingWithConcurrentMark 默认开启

##### 04_4_9 JDK 8u60 回收巨型对象

- 一个对象大于region的一半时，被称为巨型对象。
- G1不会对巨型对象进行复制（太大，复制算法好费时间）
- 回收优先考虑。
- G1会跟踪老年代所有的incoming引用，老年代引用为0时的巨型对象就可以在新生代的垃圾回收中被处理。

##### 04_4_10 JDK9并发标记起始时间调整

- 并发标记必须在堆空间沾满之前完成，否则触发FULL GC
- JDK9之前使用-XX:InitiatingHeapOccupancyPercent
- JDK9 可以去动态调整
  - -XX:InitiatingHeapOccupancyPercent设置初始值
  - 进行数据采样并动态调整
  - 总会添加一个安全的空档空间

官方调优文档地址：https://docs.oracle.com/en/java/javase/14/gctuning/introduction-garbage-collection-tuning.html#GUID-326EB4CF-8C8C-4267-8355-21AB04F0D304

接下来的内容请见[http://www.codeww.cn/archives/jvm%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0%E4%B9%8B%E4%BA%8C](http://www.codeww.cn/archives/jvm学习笔记之二)

[ JVM学习笔记之二](http://ftp6269263.host103.abeiyun.cn/archives/jvm学习笔记之二)[二分查找（多个返回多下标） ](http://ftp6269263.host103.abeiyun.cn/archives/二分查找多个返回多下标)

相关标签

[JAVA](http://ftp6269263.host103.abeiyun.cn/tags/java)

[基础知识](http://ftp6269263.host103.abeiyun.cn/tags/基础知识)

相关推荐

[JVM内存模型](http://ftp6269263.host103.abeiyun.cn/archives/jvm内存模型)[JVM学习笔记](http://ftp6269263.host103.abeiyun.cn/archives/jvm学习笔记)[JVM学习笔记之二](http://ftp6269263.host103.abeiyun.cn/archives/jvm学习笔记之二)

CodeZX @2020 **Thank for [Halo](https://github.com/halo-dev/halo)**