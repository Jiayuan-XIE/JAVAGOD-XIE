# Spring Boot

一切框架的产生，都是为了简化搭建和开发的过程，让程序员能更加专注于业务流程本身，而不是与业务无关的事情。

## 1. 是什么

### 1.1 Spring

是在Spring的基础上提供的一套全新的开源框架，其目的是为了简化Spring的搭建和开发过程。

**虽然 Spring 的组件代码是轻量级的，但它的配置却是重量级的（需要大量 XML 配置）** 。

即时Spring2.5提供了注解和组件扫描，但在开启Spring一些特性以及引入第三方库的时候，仍需要不少配置。



## 1.2 Spring Boot

Spring Boot 具有 Spring 一切优秀特性，Spring 能做的事，Spring Boot 都可以做，而且使用更加简单，功能更加丰富，性能更加稳定而健壮。随着近些年来微服务技术的流行，Spring Boot 也成了时下炙手可热的技术。

**它去除了大量的XML配置文件，简化了复杂的依赖管理。**

Spring Boot **集成了大量常用的第三方库配置**，Spring Boot 应用中这些第三方库几乎可以是**零配置的开箱即用**（out-of-the-box），大部分的 Spring Boot 应用都只需要非常少量的配置代码（基于 Java 的配置），开发者能够更加专注于业务逻辑。 



## 2. RESTful项目

RESTFUL是一种网络应用程序的设计风格和开发方式，基于[HTTP](https://baike.baidu.com/item/HTTP/243074)，可以使用[XML](https://baike.baidu.com/item/XML/86251)格式定义或[JSON](https://baike.baidu.com/item/JSON/2462549)格式定义。

它与传统的MVC模式开发最大的区别就是响应的内容的创建方式：

**传统的 MVC 模式开发会直接返回给客户端一个视图，但是 RESTful Web 服务一般会将返回的数据以 JSON 的形式返回**

**这也就是现在所推崇的前后端分离开发。**



## 3. 过滤器、拦截器、监听器

### 3.1 过滤器

起到一个筛选过滤的作用，将多个事件中符合要求的给过滤出来，进行处理。

权限管理，过滤掉没有登陆或者权限不符的URL并重定向到登陆页面。



### 3.2 拦截器（写）

想要对到来的某个进行的流程进行干预，甚至终止它的进行

对事件本身做一个写的操作



### 3.3 监听器（读）

当一个事件发生的时候，你希望获得这个事件发生的详细信息，而**并不想干预**这个事件本身的进程，这就要用到监听器。

如通知公告，工单审批，事件发送的时候，拿到用户的信息，并发送短信。



![img](http://note.youdao.com/yws/public/resource/7d81e6a39024a96dd86efacf29f4ca80/xmlnote/WEBRESOURCE3a605e35918e4c42bb4835c959aac77f/2011)



## 4. 自动装配原理

Spring Boot的自动配置是基于Spring Factories机制实现的

它是一种服务发现机制，Spring Boot 会自动扫描所有 Jar 包类路径下 **META-INF/spring.factories** 文件，并读取其中的内容，进行实例化，这种机制也是 Spring Boot Starter 的基础。

