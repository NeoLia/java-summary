[TOC]

# 1. JMM示意图

![#](E:\study-summary\image\Java内存模型.png)

# 2. 类和对象

 [Oop-Klass体系（HotSpot JVM）.pdf](file\Oop-Klass体系（HotSpot JVM）.pdf)

# 3. 内存回收（HotSpot为例）

## 3.1. 关注的内存区域

内存空间动态变化的区域，如堆、方法区（JDK1.8移除，元空间代替）

## 3.2. 内存回收的方法论——垃圾回收算法

1. 标记/清除算法（基础）

2. 复制算法

   适合对象存活率低的区域，应用在年轻代上，将年轻代分为Eden、From Survivor、To Survivor（保留空间），默认比例为8:1:1。

3. 标记/整理算法

4. 分代收集算法

   将堆分为年轻代(young)、老年代(tenured/old)、永久代(perm)，分别应用不同的垃圾回收算法

## 3.3.内存回收的具体实现——GC（垃圾收集器）

### 3.3.1. 年轻代GC

1. Serial

   基础，单线程，运行GC线程需要先停止其它所有线程

2. ParNew

   多线程

3. Parallel Scavenge

   采用复制算法，多线程，注重吞吐量

   吞吐量 = 代码运行时间 / （代码运行时间 + 垃圾收集时间）

### 3.3.1. 老年代GC

1. Serial Old

   采用标记/整理算法，单线程

2. Parallel Old

   采用标记/整理算法，多线程

3. CMS

   采用标记/清除算法



### 3.3.1. 特殊GC

G1



