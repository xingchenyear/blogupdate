

date: 2018-01-27 10:53:31
tags:
---

## Spring相关资料

> Spring 学习资料
http://spring.io/  官网首页
http://projects.spring.io/spring-framework/   aop ico文档

<!--more-->

## Spring是什么

* Spring是一个开源框架，为了解决企业应用开发的复杂性而创建的，现在已经不止应用于企业应用
* 是一个轻量级的IOC(控制反转)和AOP(面相切面编成)的容器框架

>- 从大小与开销两方面而言Spring都是轻量级的
>- 通过IOC(控制反转)的技术达到松耦合的目的
>- 提供了AOP的丰富支持，允许通过分离应用的业务逻辑与系统级服务进行内聚性开发
>- 包含并管理应用对象的配置和生命周期，这个意义上是一种容器
>- 将简单的组件配置、组合成为复杂的应用，这个意义上是框架

## Framework Runtime

> ![](https://vking.top/img/Spring01.png)

## 什么是IOC

>- IOC:控制反转，控制权的转移，应用本身不负责以来对象的创建和维护，而是由外部容器负责创建和维护
>- DI(依赖注入)是其一种实现方式
>- 目的：创建对象并且组装对象之间的关系

## 单元测试

>- 下载junit-*.jar并引入工程
>- 创建UnitTestBase类，完成对Spring配置文件的加载、销毁
>- 所有的单元测试类都继承自UnitTestBase，通过它的getBean方法取得想要得到的对象
>- 子类(具体执行单元测试的类)加注解:@RunWith(BlockJunit4ClassRunner.class)
>- 单元测试方法加注解:@Test
>- 右键选择要执行的单元测试方法执行或者执行一个类的全部单元测试方法

## Bean容器的初始化

- 基础:两个包

    - org.springframework.beans
    - org.springframework.context
    - BeanFactory提供配置结构和基本功能，加载并初始化Bean
    - ApplicationContext保存了Bean对象并在Spring中广泛使用
- 方式：ApplicationContext
    - 本地文件
    - Classpath
    - Web应用中以来servlet或Listener

## Spring的注入

- Spring注入指在启动Spring容器加载bean配置的时候，完成对变量的赋值行为
- 常用的两种注入方式
    - 设值注入
    - 构造注入      
## Bean容器的初始化

- 基础:两个包

    - org.springframework.beans
    - org.springframework.context
    - BeanFactory提供配置结构和基本功能，加载并初始化Bean
    - ApplicationContext保存了Bean对象并在Spring中广泛使用
- 方式：ApplicationContext
    - 本地文件
    - Classpath
    - Web应用中以来servlet或Listener

## Spring的注入

- Spring注入指在启动Spring容器加载bean配置的时候，完成对变量的赋值行为
- 常用的两种注入方式
    - 设值注入
    - 构造注入

### 设置注入

```bash
XML配置
 <bean id="ispringService" class="com.DIDemo.service.springServiceImopl">
        <property name="ispringDao" ref="ispringDao"></property>
 </bean>
 <bean id="ispringDao" class="com.DIDemo.dao.sptingDaoImpl"></bean>
```
### 构造注入
```bash
<bean id="injectionService" class="com.imooc.ioc.injection.service.InjectionServiceImpl">
        	<constructor-arg name="injectionDAO" ref="injectionDAO"></constructor-arg>
        </bean>
        
        <bean id="injectionDAO" class="com.imooc.ioc.injection.dao.InjectionDAOImpl"></bean>
```
## bean的配置项

```bash
id:bean的唯一标示
class:需要实例化的类
scope:范围(作用域)
constructor arguments:构造器参数
properties:属性
autowiring mode:自动装配模式
lazy-initialization mode:懒加载模式
initialization/destruction method:初始化及销毁的方法
```
### bean的作用域

```bash
singleton:单例，指一个Bean容器中只存在一份（默认）
prototype:每次请求都创建新的实例，destroy方式不生效
request:每次http请求被创建一个实例且仅在当前request中有效
session:同上,每次http请求被创建，当前session有效
global session:基于portlet的web中有效（portlet定义了global session），如果在web中同session
```

### Bean初始化
```bash
1.实现org.spppringframework.beans.factory.InitialingBean接口，覆盖afterPropertiesSet方法
2.在配置文件中配置init-method
```
### Bean销毁

```bash
1.实现org.spppringframework.beans.factory.DisposableBean接口覆盖destroy方法
2.在配置文件中配置destroy-method
```

### 配置全局默认初始化销毁方法

```bash
在Bean全局配置文件中添加default-init-metho=="init"初始化方法
default-destroy-method="destroy"
```

### Resources
```bash
针对资源文件的统一接口
UrlResource:URL对应的组员，根据一个URL地址即可构建
ClassPathResource：获取类罗京下的资源文件
FileSystemResource：获取文件系统里面的资源
SercletCentextRescource：ServletContext封装的资源用于访问ServletContext环境下的资源
InputStreamResource：针对于输入流封装的资源
ByteArrayResource：针对字节数组封装资源
```


