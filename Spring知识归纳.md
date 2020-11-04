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

# 2. Spring Bean

## 2.1. IOC Container的启动初始化过程



## 2.3. 实例化Bean的方式

1. 构造器
2. 静态工厂方法
3. 实例工厂方法

## 2.2. 创建Bean的过程

## 2.2. 依赖注入

### 2.2.1. 依赖注入的方式

1. 构造器
2. setter方法
3. 静态工厂方法
4. 实例工厂方法

### 2.2.2. @Autowired和@Resource的区别

相同点：

1. 都可以修饰属性、setter方法；

区别：

1. @Autowired属于Spring；@Resource属于JavaEE。
2. @Autowired可以修饰constructor，@Resource不可以修饰constructor。
3. @Autowired默认按bean类型装配，当Spring上下文中有多个同类型的bean，需要与@Qualify结合使用，指定bean的id；@Resource默认按bean名称装配（通过指定name属性，不指定则按属性名称找），找不到则按bean类型装配。

# 3. Spring事务

## 3.1. 声明式事务管理@Transactional

### 3.1.1. 创建@Transactional方法代理对象的过程

1. BeanPostProcessor
2. AnnotationAwareAspectJAutoProxyCreator extends BeanPostProcessor
3. AnnotationAwareAspectJAutoProxyCreator.postProcessAfterInstantiation()， return 代理对象
4. BeanFactoryTransactionAttributeSourceAdvisor

### 3.1.2. 调用@Transactional方法的过程

1. 调用@Transactional方法

2. 调用代理对象方法

   CglibAopProxy#**DynamicAdvisedInterceptor**.intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy)

   或者

   JdkDynamicAopProxy.invoke(Object proxy, Method method, Object[] args)

3. 内部调用**TransactionInterceptor**.invoke(MethodInvocation invocation)，TransactionInterceptor extends TransactionAspectSupport

4. 内部调用**TransactionAspectSupport**.invokeWithinTransaction(Method method, @Nullable Class<?> targetClass, TransactionAspectSupport.InvocationCallback invocation)：是否创建事务，开始事务、执行目标方法、提交/回滚事务

## 3.1.2. 注意事项

1. @Transactional只能使用在public方法上

2. 自调用问题：

   （1）没有@Transactional注解的方法，其内部调用另一个@Transactional的方法时，不执行事务。
   
   （2）外部方法有@Transactional，其内部调用的另一方法没有@Transactional，外部方法回滚，内部调用方法也会回滚。
   
   （3）外部方法决定内部调用方法。

## 3.2. 事务传播类型(Propagation)

### 3.2.1. 支持使用当前事务

1. REQUIRED（默认）

   如果当前事务不存在，则创建一个新事务。

2. SUPPORTS

   如果当前事务不存在，则不使用事务。

3. MANDATORY

   如果当前事务不存在，则抛出Exception。

### 3.2.2.不支持使用当前事务

1. REQUIRES_NEW

   创建一个新事务，如果当前事务存在，则挂起当前事务。

2. NOT_SUPPORT

   无事务执行，如果当前事务存在，则挂起当前事务。

3. NEVER

   无事务执行，如果当前事务存在，则抛出Exception。

4. NESTED

   嵌套事务，如果当前事务存在，那么在嵌套的事务中执行。如果当前事务不存在，则表现跟REQUIRED一样。

## 3.3. 事务隔离级别

### 3.3.1. READ_UNCOMMITED

脏读、不可重复读、幻读

### 3.3.2. READ_COMMITED

不可重复读、幻读

### 3.3.3. REPEATABLE_READ

幻读

### 3.3.3. SERIALIZABLE



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

## 4.3. Spring AOP的实现

### 4.3.1. CglibAopProxy

1. CglibAopProxy$DynamicAdvisedInterceptor.intercept()

### 4.3.1. JdkDynamicAopProxy