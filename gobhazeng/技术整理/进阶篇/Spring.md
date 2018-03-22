# Spring

## Sping是一个开源框架

Spring为简化企业级应用开发而生（与之对应的重量级别企业级映射EJB）。使用Spring可以使简单的JavaBean实现以前只有EJB才能实现的功能。

**Spring是一个loC（Dl）和AOP容器框架**

- loC(DI)：loC控制反转，DI依赖注入（控制反转就是把控制的权利移交出去，针对对象的创建交给Spring容器框架来完成）

- AOP：面向切面编程，对OOP(面向对象编程)的补充

- 容器：容器是为了管理Bean的生命周期

## Spring的特点

- 轻量级：Spring是非侵入性的-基于Spring开发的应用中的对象可以不依赖于Spring的API

- 依赖注入：DI-dependency injection、IOC

- 面向切面编程：AOP-aspect oriented programming

- 容器：Spring是一个容器，因为它包含并且管理应用对象的生命周期

- 框架：Spring实现了使用简单的组建配置组合成一个复杂的应用，在Spring中可以使用XML和Java注解组合这些对象

- 一站式：在IOC和AOP的基础上可以整合各种企业应用的开源框架和优秀的第三方类库（实际上Spring自身也提供了展示层SpringMVC和持久层的SpringJDBC）