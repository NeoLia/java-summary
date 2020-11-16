[TOC]

# 1. IOC

## 1.1. IOC是什么



IOC(Inverse of Control)——控制反转，是一种设计理念。指的是将对象生命周期的管理（创建、销毁）和对象间依赖关系的维护（注入对象到另一个依赖它的对象）从代码（被注入对象）转移到IOC容器中去，将对象的管理维护工作从自己的代码中**解耦**出来。Bean（组件）之间的依赖关系由容器在应用系统**运行期**来决定。

1. 传统编程中，一个对象通过new创建它依赖的对象，依赖的对象由**它自己**控制；在IOC中，一个对象从IOC容器中被动接受它依赖的对象，依赖的对象由**IOC容器**控制。

2. 控制的东西是**对象**。
3. 正转：一个对象主动创建它依赖的对象；反转：一个对象从IOC容器中被动接受它依赖的对象，IOC容器创建对象并注入到依赖它的对象中去。依赖对象的获取从**主动变为被动**。



## 1.2. Spring IOC的实现方式

Spring的IOC使用了两个实现方式，**依赖注入**(DI)和**依赖查找**(DS)。

依赖注入：IOC容器自动将它管理的对象注入到需要的地方。如：setter,、constructor、静态工厂方法、动态工厂方法、~~接口实现注入~~

依赖查找：对象内部使用IOC容器的API获取它依赖的对象。如：BeanFactory.getBean(...)

## 1.3. Spring IOC体系

### 1.3.1. Resource (I)

资源的抽象，不同的实现代表不同的**资源访问策略**，如ClasspathResource、URLResource、FileSystemResource等

### 1.3.2. BeanFactory (I)

BeanFactory是Spring IOC容器的**核心接口**、也是**顶层接口**，定义了IOC容器的基本功能(getBean, getBeanProvider, containsBean, isSingleton, isPrototype, isTypeMatch, getType, getAliases)。

### 1.3.3. ApplicationContext (I)

ApplicationContext是BeanFactory的子接口，继承BeanFactory的所有功能，并提供了许多有用的扩展功能，直接父接口是ListableBeanFactory。

### 1.3.4. BeanDefinition (I)

描述Spring Bean的对象。

### 1.3.5. BeanDefinitionReader (I)

读取Spring的配置文件的内容，将其转换为BeanDefinition。

### 1.3.6. BeanFactory和ApplicationContext的差别

1. 因为**BeanFactory**采用**延迟加载**的方式实例化Bean，当第一次使用到某个Bean时才会实例化它，所以Spring IOC初始化启动时只初始化IOC容器，不会实例化Bean。好处是启动速度快，内存消耗较小；坏处是不能在初始化启动时检查配置是否有问题。
2. 因为**ApplicationContext**采用**即时加载**的方式实例化Bean，所以在Spring初始化启动时会先初始化IOC容器，再实例化所有Bean。好处是能够给在初始化启动时检查配置是否有问题，坏处是启动速度慢，内存消耗较大。
3. ApplicationContext比BeanFactory加入了**更多**对开发者有用的**功能**。BeanFactory的许多功能需要通过**编程**实现，而 ApplicationContext可以通过**配置**实现。BeanFactory主要面对Spring框架，ApplicationContext则为普通开发者所使用。

## 1.4. 注意事项

### 1.4.1. 两个Bean互相依赖

两个Bean之间互相依赖，为了防止出现依赖注入问题，必须使用setter方法来注入依赖。

### 1.4.2. Spring的IOC容器与Spring MVC的IOC容器

Spring的IOC容器和Spring MVC的IOC容器不是同一个容器，但存在父子容器的关系。Spring的IOC容器是Spring MVC的IOC容器的父容器，子容器可以获取父容器的Bean，但父容器不可以获取子容器的Bean。

# 2. Spring Bean



## 2.2. 实例化Bean的方式

1. 构造器
2. 静态工厂方法
3. 实例工厂方法

## 2.3. 依赖注入

### 2.3.1. 依赖注入的方式

1. 构造器
2. setter方法
3. 静态工厂方法
4. 实例工厂方法

### 2.3.2. 自动装配的配置方式

1. 通过xml（显式装配）
2. 通过注解@Autowired或@Resource（隐式装配）

### 2.3.3. @Autowired和@Resource的区别

相同点：

1. 都可以修饰属性、setter方法；

区别：

1. @Autowired属于Spring；@Resource属于JavaEE。
2. @Autowired可以修饰constructor，@Resource不可以修饰constructor。
3. @Autowired默认按bean类型装配，当Spring上下文中有多个同类型的bean，需要与@Qualify结合使用，指定bean的id；@Resource默认按bean名称装配（通过指定name属性，不指定则按属性名称找），找不到则按bean类型装配。

## 2.4. @Value

1. 语法

   @Value("${propertyName : defaultValue}")

   @Value("#{obj.propertyName?: defaultValue}")

2. 区别

   (1)@Value("${propertyName : defaultValue}")

   从加载到spring上下文的properties文件中读取数据

   (2)@Value("#{obj.property ?: defaultValue}")

   从加载到springIOC容器的Bean中读取数据

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

## 3.2. 事务传播行为(Propagation)

### 3.2.1. 支持使用当前事务

1. REQUIRED（默认）

   如果当前事务不存在，则创建一个新事务。

2. SUPPORTS

   如果当前事务不存在，则不使用事务。

3. MANDATORY

   如果当前事务不存在，则抛出异常。

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

   根据PointCut将Advice插入到指定的JointCut并创建代理对象的过程，可以发生在**编译时、加载时或运行时**。

   发生在**编译时**，需要有支持AOP的编译器(AspectJ)；发生在**加载时**，需要有支持AOP的类加载器；发生在**运行时**，直接通过**反射**和**动态代理**机制实现。

8. Introduction引入

   在不改变代码的前提下，在运行时为类动态的加上属性或方法。

9. Target Object目标对象

   被代理的目标对象，被织入的对象模块，只包含业务逻辑的对象，等待切面切入。

10. Proxy Object代理对象

    目标对象被通知插入后的对象，通常目标对象和代理对象都实现了共同的接口或父类。

## 4.3. Advice执行顺序

### 4.3.1. 同一Aspect，不同Advice

1. around before => before => target method => around after => after => after returning
2. around before => before => target method => around after => after => after throwing

### 4.3.2. 不同Aspect，不同Advice

1. 先确定Aspect的优先级

2. **高优先级**Aspect的Advice先执行入操作入栈（Around Before, Before），低优先级Aspect的Advice随后

3. **低优先级**Aspect的Advice限制性出操作出栈(Around After, After, After Returning / After Throwing)，高优先级Aspect的Advice随后

   （先入后出）

### 4.3.3. 同一Aspect，同一Advice

"同一Aspect，相同Advice的执行顺序"并不能直接确定，而且 **@Order** 在Advice方法上也无效，但是有如下两种变通方式：

1. 将两个 Advice 合并为一个 Advice，那么执行顺序就可以通过代码控制了
2. 将两个 Advice 分别抽离到各自的 Aspect 内，然后为 aspect 指定执行顺序

## 4.4. Spring AOP的实现

### 4.4.1. CglibAopProxy

1. CglibAopProxy$DynamicAdvisedInterceptor.intercept()

### 4.4.2. JdkDynamicAopProxy

# Spring实现涉及的原理

1. 工厂模式（抽象工厂模式、工厂方法模式）：BeanFactory、ApplicationContext
2. 代理模式：AOP
3. 模板方法：JdbcTemplate
4. Java反射