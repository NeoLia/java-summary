[TOC]

# 1. IOC

## 1.1. IOC是什么

IOC(Inverse of Control)——控制反转，指的是将对象的创建、销毁、依赖关系的维护从代码转移到外部容器上，使代码从管理对象的工作中解耦出来，是一种设计理念。

## 1.2. Spring IOC的实现

Spring的IOC有两个实现，依赖注入(DI)和依赖查找(DS)。

依赖注入：@Autowired, @Resource，setter，constructor，静态工厂方法、动态工厂方法

依赖查找：BeanFactory.getBean("")

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



