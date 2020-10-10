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

