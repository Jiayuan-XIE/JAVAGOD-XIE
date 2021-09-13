## 1. MVC设计模式

### 1.1 是什么

MVC 设计模式一般指 MVC 框架，M（Model）指数据模型层，V（View）指视图层，C（Controller）指控制层。

使用 MVC 的**目的**是将 **M 和 V 的实现代码分离**，**使同一个程序可以有不同的表现形式**。其中，View 的定义比较清晰，就是用户界面。

Spring MVC 是 Spring 提供的一个基于 MVC 设计模式的轻量级 Web 开发框架，本质上相当于 Servlet。

**Spring MVC 是结构最清晰的 Servlet+JSP+JavaBean 的实现**，是一个典型的教科书式的 MVC 构架，不像 Struts 等其它框架都是变种或者不是完全基于 MVC 系统的框架。

Spring MVC 角色划分清晰，分工明细，并且和 Spring 框架无缝结合。Spring MVC 是当今业界最主流的 Web 开发框架，以及最热门的开发技能。

### 1.2 优点

- 提高了代码的**可重用性**，多视图共享一个模型
- 松耦合架构，提高了系统的可扩展和可维护性
- 有利于软件工程化管理

### 1.3 缺点

- 提高了系统的复杂性，增加了开发工作量
- 效率损失，视图对模型数据的低效率访问







## 2. Spring MVC执行流程

![Spring MVC执行流程](http://note.youdao.com/yws/public/resource/7d81e6a39024a96dd86efacf29f4ca80/xmlnote/WEBRESOURCE39ac867081fe4a4fbcea01727b5f579a/2008)

### 2.1 流程

#### 简化流程

1. 用户的请求会被提交到DispatcherServlet派发器；
2. 派发器请求 **HandlerMapping**（处理器映射器），**并返回一个执行链；**
3. 派发器请求**HandlerAdapter**，然后去执行相应的**Handler**；
4. 执行返回**ModelAndView**后，请求视图解析器进行解析，返回相应的View；
5. 派发器将Model 中的模型数据填充到 View 视图中进行渲染；



#### 具体流程：

SpringMVC 的执行流程如下。

1. 用户点击某个请求路径，发起一个 HTTP request 请求，该请求会被提交到 **DispatcherServlet**（前端控制器）；
2. 由 DispatcherServlet 请求一个或多个 **HandlerMapping**（处理器映射器），**并返回一个执行链**（HandlerExecutionChain）。
3. DispatcherServlet 将执行链返回的 Handler 信息发送给 **HandlerAdapter**（处理器适配器）；
4. HandlerAdapter 根据 Handler 信息找到并执行相应的 **Handler**（常称为 Controller）；
5. Handler 执行完毕后会返回给 HandlerAdapter 一个 **ModelAndView** 对象（Spring MVC的底层对象，包括 Model 数据模型和 View 视图信息）；
6. HandlerAdapter 接收到 ModelAndView 对象后，将其返回给 DispatcherServlet ；
7. DispatcherServlet 接收到 ModelAndView 对象后，会请求 **ViewResolver**（视图解析器）对视图进行解析；
8. ViewResolver 根据 View 信息匹配到相应的视图结果，并返回给 DispatcherServlet；
9. DispatcherServlet 接收到**具体的 View 视图后，进行视图渲染**，将 Model 中的模型数据填充到 View 视图中的 request 域，生成最终的 View（视图）；
10. 视图负责将结果显示到浏览器（客户端）。

