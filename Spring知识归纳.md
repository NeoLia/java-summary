[TOC]

# 1. IOC

## 1.1. IOC是什么

IOC(Inverse of Control)——控制反转，指的是将对象的创建、销毁、装配、依赖关系的维护等从代码转移到外部容器上，使代码从管理对象的工作中解耦出来，是一种设计理念。

## 1.2. Spring IOC的实现

Spring的IOC有两个实现，依赖注入(DI)和依赖查找(DS)。

依赖注入：@Autowired, @Resource，setter，constructor，静态工厂方法、动态工厂方法

依赖查找：BeanFactory.getBean("")、@Resource

## 1.3. Spring IOC容器

Spring的IOC容器负责创建对象、销毁对象、维护对象间的依赖关系。

Spring的IOC容器和Spring MVC的IOC容器不是同一个容器，但存在父子容器的关系。Spring的IOC容器是Spring MVC的IOC容器的父容器，子容器可以获取父容器的bean，但父容器不可以获取子容器的bean。

## 1.4. 两个bean互相依赖

两个bean之间互相依赖，为了防止出现依赖注入问题，必须使用setter来注入依赖。

# 2. 装配bean

## 2.1. @Autowired和@Resource

相同点：

1. 都可以修饰属性、setter方法；

区别：

1. @Autowired属于Spring；@Resource属于JavaEE。
2. @Autowired默认按bean类型装配，当Spring上下文中有多个同类型的bean，需要与@Qualify结合使用，指定bean的id；@Resource默认按bean名称装配（通过指定name属性，不指定则按属性名称找），找不到则按bean类型装配。

# 3. Spring事务

## 3.1. 声明式事务管理@Transactional

## 3.1.1 组成部分

1. EntityManager代理对象

2. TransactionInterceptor事务切面（环绕切面）

   "before"时，调用TransactionManager方法，判断被调用的业务方法是在正在运行的事务上执行，还是创建一个新的独立事务并在新事务上执行。

   "after"时，判断事务是被提交、回滚，还是继续运行。

3. TransactionalManager事务管理器

   （1）判断新的EntityManager是否应该被创建

   （2）判断是否开始新的事务

## 3.1.2. 不生效的情况

1. private方法

2. 同一个bean里，嵌套的public方法

   tips：通常在service方法或service类中使用，如果涉及到第三方资源的访问可能会消耗较长的时间，可以拆分出一个单独的service来访问。

# 4. AOP

## 4.1. 关系图

![img](https://upload-images.jianshu.io/upload_images/4933701-e3d88528ee84027c.png?imageMogr2/auto-orient/strip|imageView2/2/w/484/format/webp)





## 4.2. 相关概念

1. 核心关注点

   应用程序中的业务逻辑处理。

2. 横切关注点

   常常出现在许多核心关注点的一些相似的地方，如日志记录、事务管理、权限认证等与业务无关的逻辑处理。

3. Joint Point连接点

   程序执行过程中明确的点，一般是方法调用，连接点就是切面切入的地方。

4. Advice通知

   在连接点上做的增强处理，分为Before、After、After Returning、After Throwing、Around。

   Before：方法调用前

   After：方法调用完成后，不管方法最后是正常返回还是抛出异常

   After Returning：方法调用完成并return后

   After Throwing：方法调用过程中抛出异常后

   Around：在目标方法调用前和完成后都都做一次增强处理

5. PointCut切入点

   定义了连接点与通知的关联，每个通知需要出现在哪些连接点上

6. Aspect切面

   应用程序中公共的非业务逻辑处理模块，若日志模块、权限模块、事务模块。

   通常是一个类，定义了多个切入点和通知，是对横切关注点的抽象。

7. Weave织入

   根据PointCut将Advice插入到指定的JointCut并创建代理对象的过程，可以发生在编译时、类加载时或运行时。

   发生在编译时，需要有支持AOP的编译器；发生在类加载时，需要有支持AOP的类加载器；发生在运行时，直接通过反射和动态代理机制实现。

8. Introduction引入

   在不改变代码的前提下，在运行时为类动态的加上属性或方法。

9. Target Object目标对象

   被代理的目标对象，被织入的对象模块，只包含业务逻辑的对象，等待切面切入。

10. Proxy Object代理对象

    目标对象被通知插入后的对象，通常目标对象和代理对象都实现了共同的接口或父类。