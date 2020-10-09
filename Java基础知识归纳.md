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

# 2. 类

# 3. 对象

## 3.1.  对象的三大特性

1. 标识

   对象的唯一表示，和每个对象的内存地址有关。

2. 状态

   对象的数据，实例域/实例变量/成员变量

3. 行为

   对数据的操作，实例方法/实例函数

## 3.2.  对象的组成

### 3.2.1. 对象头

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

### 3.2.2. 实例数据

对象的属性和它们的值。

### 3.2.3. 对齐填充字节

因为JVM要求对象所占的内存大小为8bit的倍数，不满足条件会填满。



# 4. 泛型

## 4.1. 类型擦除

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



## 4.2. 共变数组

数组的共变性即协变性，指的是当类Super是类Sub的父类时，Super变量是可以引用Sub对象的，而且Super[]变量也能引用Sub[]对象。但**泛型不允许**。

## 4.3. 类型参数T与通配符?的区别

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

   

# 5. 多线程

## 5.1. 锁升级(since JDK1.6)

设有一对象object，两线程T1、T2：

1. 当object被用作synchronized关键字的同步锁，但还没有任何线程来尝试获取锁时，还是一个普通的对象；

2. **偏向锁**：T1尝试获取对象锁时，首先检查object对象头的Mark Word的thread id，发现为空，就将T1的线程ID写入对象头的Mark Word的thread id里，T1获得对象锁，此时是偏向锁；

3. T1再次获取对象锁时，首先检查object对象头的Mark  Word的thread id，发现thread id和T1的线程id相同，T1获得对象锁；

4. **轻量级锁**：T2尝试获取对象锁，首先检查object对象头的Mark Word的thread id，发现thread id和T2的线程id不同，使用CAS尝试获得锁。

   成功则将object对象头的Mark Word的thread id改为T2的线程id；失败则将偏向锁升级为轻量级锁；

5. 偏向锁升级为轻量级锁时，JVM会在T2的线程栈区中开辟一块单独空间，使用CAS保存指向object对象头的Mark Word的指针pointer；object对象头的Mark Word会使用CAS保存pointer。保存成功则T2获得对象锁，保存失败则使用自旋锁尝试抢锁；

6. **自旋锁**：在阻塞边界做忙循环，尝试一定次数的抢锁行为。JVM会为T2使用自旋尝试抢锁object，自旋次数由JVM决定，抢锁成功则执行同步代码，抢锁失败则将轻量级锁升级为重量级锁；

7. **重量级锁**：T2进入阻塞状态。

## 5.2. 线程调度模型

线程调度模型主要分为分时调度模型（时间片轮转）和抢占式调度模型。Java采用抢占式调度模型，优先级高的线程能够比优先级低的线程先执行。

但代码中不应该依赖线程优先级来控制程序逻辑，因为基于优先级的线程调度依赖于具体操作系统的线程调度器服务的实现，有的操作系统不是高优先级线程优先执行的。

## 5.3. 线程优先级

1. Java线程优先级的范围1-10，1最小、10最大，默认优先级是5；
2. 子线程的优先级继承父线程的；
3. 线程优先级具有随机特性，即低优先级线程不一定会要在高优先级线程执行完才开始执行。

# 6. 代理

## 6.1. 静态代理



## 6.2. 动态代理

# 7. 集合容器

## 7.1. List

### 7.1.1. ArrayList和Vector的区别



### 7.1.2. ArrayList和LinkedList的区别



## 7.2. Map

### 7.2.1. HashMap和Hashtable的区别



## 7.3. Set

### 7.3.1. HashSet和HashMap的关系





