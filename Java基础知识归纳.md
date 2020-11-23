[TOC]

# 1. 语言特性

## 1.1. 语言描述

1. Java是面向对象的程序设计语言，Java的特性是封装、多态、继承；
2. 面向对象编程语言的特性是抽象、封装、多态、继承；  

## 1.2. 特性描述

1. 封装

   通过访问权限控制，隐藏对象的数据和数据操作，仅提供有限的公共接口给外部访问和调用。

2. 多态

   编译时多态——方法重载，方法名相同但方法的参数排序/参数类型/参数个数不同，在编译时就会检查调用的方法是哪一个，所以是编译时多态。

   运行时多态——方法重写/覆盖，子类重写父类的方法签名相同的方法，访问修饰符可以改变为比父类的范围更大的访问修饰符，但不能修改为访问范围更小。原因：里氏替换原则——父类能够使用的地方，其子类也能够使用。

3. 继承

   使用已存在的类的定义作为基础，建立新类的技术。Java支持单继承类、多实现接口。

# 2. 类和对象

## 2.1.  对象的三大特性

1. 标识

   对象的唯一表示，和每个对象的内存地址有关。

2. 状态

   对象的数据，实例域/实例变量/成员变量

3. 行为

   对数据的操作，实例方法/实例函数

## 2.2.  对象的组成

### 2.2.1. 对象头

1. Mark Word

   当对象被synchronized关键字当作同步锁时，围绕这个锁的的一系列操作都和Mark Word有关。

   Mark Word的大小由JVM决定，在32位JVM中的长度是32bit，在64位JVM中的长度是64bit。

   当对象是普通对象时，Mark Word主要保存Hash Code；

   当对象被当作同步锁时：

   1. 偏向锁：主要保存线程id
   2. 轻量级锁：主要保存的是指向轻量级锁的指针
   3. 重量级锁：主要保存的是指向重量级锁的指针

2. 指向Class的指针

   该指针指向存储在方法区/元空间(since JDK1.8)中的对象所属的类信息。

   该指针的大小由JVM决定，在32位JVM中的长度是32bit，在64位JVM中的长度是64bit。

3. *数组长度（数组对象才有）

   数组长度在32位和64位JVM中长度都是32bit。

### 2.2.2. 实例数据

对象的属性和它们的值。

### 2.2.3. 对齐填充字节

因为JVM要求对象所占的内存大小为8bit的倍数，不满足条件会填满。

## 2.3. 对象的内存结构

类——Klass(InstanceKlass)

对象——Oop(Ordinary object pointer)

## 2.4. 对象的访问方式

1. 通过句柄访问

   ![reference变量通过句柄访问对象](E:\study-summary\images\reference变量通过句柄访问对象.png)

   优点：稳定

   reference变量指向句柄池中的一个句柄，这个句柄指向堆中的对象（实例数据）和对象所属类信息（对象类型数据）。

2. 通过直接指针访问

   ![reference变量通过直接指针访问对象](E:\study-summary\images\reference变量通过直接指针访问对象.png)

   优点：访问速度快，实现的JVM有HotSpot（最流行的JVM）。

   reference变量指向堆中的对象（实例数据），里面包含指向对象所属类信息（对象类型数据）的指针。

# 3. 泛型

## 3.1. 类型擦除

类型参数T在运行时会全部变成Object，只会在编译时进行类型检查，类型参数的实际类型由动态类型决定，如：

```
public class Container<T extends CharSequence> {
	private T ele;
	
    public Container(T e) {
    	this.ele = e;
    }
    
    public T getEle() {
    	return ele;
    }
}

public static void main(String[] args) {
    // 类型参数的实际类型由动态类型决定，即字符串""，所以ele是String类型
	Container<StringBuilder> container = new Container(""); 
	// 获得Class<String>
	container.getEle().geClass();
}
```



## 3.2. 共变数组

数组的共变性即协变性，指的是当类Super是类Sub的父类时，Super变量是可以引用Sub对象的，而且Super[]变量也能引用Sub[]对象。但**泛型不允许**。

## 3.3. 类型参数T与通配符?的区别

T表示确定的类型，?表示不确定的类型。

1. T可以保证类型参数的一致性，?不可以。

   ```
   // 能够保证dest和src具有相同的元素类型
   public <T extends Number> void test(List<T> dest, List<T> src) {}
   
   // 不能够保证dest和src具有相同的元素类型
   public void test(List<? extends Number> dest, List<? extends Number> src) {}
   ```

2. 类型参数可以多重限定而通配符不行。

   ```
   public <T extends A & B> void test(T t) {}
   ```

3.  通配符可以使用超类限定而类型参数不行

   ```
   T extends A
   
   ? extends A
   ? super A
   ```

   

# 4. 多线程

## 4.1. 锁升级(since JDK1.6)

设有一对象object，两线程T1、T2：

1. 当object被用作synchronized关键字的同步锁，但还没有任何线程来尝试获取锁时，还是一个普通的对象；

2. **偏向锁**：T1尝试获取对象锁时，首先检查object对象头的Mark Word的thread id，发现为空，就将T1的线程ID写入对象头的Mark Word的thread id里，T1获得对象锁，此时是偏向锁；

3. T1再次获取对象锁时，首先检查object对象头的Mark  Word的thread id，发现thread id和T1的线程id相同，T1获得对象锁；

4. **轻量级锁**：T2尝试获取对象锁，首先检查object对象头的Mark Word的thread id，发现thread id和T2的线程id不同，使用CAS尝试获得锁。

   成功则将object对象头的Mark Word的thread id改为T2的线程id；失败则偏向锁升级为轻量级锁；

5. 偏向锁升级为轻量级锁时，JVM会在T2的线程栈区中开辟一块单独空间，使用CAS保存指向object对象头的Mark Word的指针pointer；object对象头的Mark Word会使用CAS保存pointer。保存成功则T2获得对象锁，保存失败则使用自旋尝试抢锁；

6. **自旋：在阻塞边界做忙循环，尝试一定次数的抢锁行为。JVM会为T2使用自旋尝试抢锁object，自旋次数由JVM决定，抢锁成功则执行同步代码，抢锁失败则将轻量级锁升级为重量级锁；

7. **重量级锁**：T2进入阻塞状态。

## 4.2. 线程调度模型

线程调度模型主要分为分时调度模型（时间片轮转）和抢占式调度模型。Java采用抢占式调度模型，优先级高的线程能够比优先级低的线程先执行。

但代码中不应该依赖线程优先级来控制程序逻辑，因为基于优先级的线程调度依赖于具体操作系统的线程调度器服务的实现，有的操作系统不是高优先级线程优先执行的。

## 4.3. 线程优先级

1. Java线程优先级的范围1-10，1最小、10最大，默认优先级是5；
2. 子线程的优先级继承父线程的；
3. 线程优先级具有随机特性，即低优先级线程不一定会要在高优先级线程执行完才开始执行。

# 5. 代理

## 5.1. 静态代理



## 5.2. 动态代理

# 6. 集合容器

## 6.1. List

### 6.1.1. ArrayList和LinkedList的区别

1. 数据结构实现：ArrayList的底层数据结构是动态数组，LinkedList的底层数据结构是双向链表。
2. 随机访问效率：ArrayList的随机访问效率比LinkedList高，因为ArrayList可以随机访问，而LinkedList只能从前向后或从后向前遍历。
3. 插入删除效率：在非首尾插入删除元素时，LinkedList的插入删除效率比ArrayList高，因为ArrayList每次插入删除都需要移动其它的元素，而LinkedList只需要改变指针的指向。
4. 内存空间占用：LinkedList比ArrayList更占内存空间，因为LinkedList的结点除了存储数据，还要存储其直接前驱结点和直接后继结点的引用。
5. ArrayList和LinkedList都是不同步、非线程安全的。

### 6.1.ArrayList和Vector的区别

二者都实现了List接口，都是有序结合

1. 线程安全：ArrayList是非线程安全的，Vector是线程安全的，因为Vector内部的方法都经过synchronized关键字修饰。
2. 性能：ArrayList的性能要优于Vector，因为Vector的同步操作需要耗费额外的时间资源。
3. 扩容：ArrayList每次扩容50%，Vector每次扩容1倍。

## 6.2. Map

### 6.2.1. HashMap和HashTable的区别

1. 线程安全： **HashMap**是非线程安全的，**HashTable** 是线程安全的，因为HashTable内部的方法都经过**synchronized**关键字修饰。

2. 性能：HashMap的性能要优于HashTable，因为HashTable的**同步操作**需要耗费额外的时间资源。

3. 对null的支持：

   在**HashMap**中，null 可以作为key，但只能有一个，可以有一个或多个key对应的value为null。

   在**HashTable**中，只要put进一个key或value为null，直接抛**NullPointerException**。

4. 初始容量和每次扩容：

   创建时不指定大小，**HashTable**默认大小为11，每次扩容后大小变为原来的2n+1。**HashMap**默认大小为16，每次扩容后大小变为原来的2倍。

   创建时指定大小，**HashTable**直接使用给定大小，**HashMap**会扩充为2的幂次方大小。

5. 底层数据结构：

   HashTable的底层数据结构是**数组+链表(拉链法)**

   HashMap的底层数据结构是**Node数组+链表/红黑树(jdk1.8**)。当**链表长度**大于阈值(默认**8**)，**数组长度**大于64，链表转换为红黑树，为了防止链表长度过长，影响搜索速度。同步时，只会锁住Node数组中链表或红黑树的**首结点**。

### 6.2.2. HashMap内部细节

1. hash值计算方式

   1次异或运算+1次位运算(hash ^ (hash >>> 16))，即高16位与低16位异或异或，目的是减少碰撞。

2. 数组(桶)下标计算方式

   index = (table.length - 1) & hash

### 6.2.3. ConcurrentHashMap

1. 并发安全：Node + CAS + Synchronized来保证并发安全进行实现，synchronized只锁定当前链表或红黑二叉树的首节点

## 6.3. Set

### 6.3.1. HashSet

HashSet底层使用HashMap存储元素，HashSet加入的元素，内部会存储在HashMap的key上，value统一存储常量PRESENT。因此HashSet与HashMap的特性差不多。

# 7. IO

## 7.1. BIO

BIO是**同步阻塞式**IO，以流的方式处理数据，效率低，适合请求连接数少、架构固定的场景，系统资源消耗大。

Java的基础IO类和Socket、ServerSocket类都是BIO。

## 7.2. NIO

NIO是对BIO的改进，是**同步非阻塞式**IO，以块的方式处理数据，效率高，适合请求连接数多、连接时间短的场景。

NIO基于**通道(Channel)**和**缓冲区(Buffer)**进行数据操作，数据总是从通道读取到缓冲区中，或者从缓冲区中写到通道中。**Selector(选择器)**用于监听多个通道的事件（比如：连接请求，数据到达等），因此使用单个线程就可以监听多个客户端通道。

1. Buffer

   是一个容器，是一个特殊的**数组**，缓冲区内部内置了一些机制，能够**跟踪和记录**缓冲区的状态和变化。Channel提供从文件、网络读取数据的渠道，但是读取或写入的数据都**必须经由Buffer**。

2. Channel

   类似于 BIO 中的 stream，用来建立到目标（文件File，网络套接字Socket，硬件设备等）的一个连接，但是需要注意：BIO 中的 stream 是单向的，例如 FileInputStream 对象只能进行读取数据的操作，而 NIO 中的通道(Channel)是双向的，既可以用来进行读操作，也可以用来进行写操作。

3. Selector

   能够检测多个注册服务上的通道是否有**事件**发生，如果有事件发生，便可以获取到事件然后**针对每个事件进行相应的处理**。这样就可以使用单线程管理多个客户端连接，这样使得只有**真正的读写事件**发生时，才会调用函数进行读写。减少了系统的开销，并且不必为每一个连接都创建一个线程，不用去维护多个线程。

## 7.3. AIO

先由操作系统完成客户端的请求，再通知服务器去启动线程进行处理。适合请求连接数多、连接时间长的场景。

## 7.4. NIO框架——Netty

Netty是用Java编写的开源框架，内部使用NIO模型，提供异步的、事件驱动的网络应用程序框架和工具，用以快速开发高性能、高可靠性的网络服务器和客户端程序。
