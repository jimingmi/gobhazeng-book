# 概论

Spring IoC的核心组件：

 - BeanFactory：提供最基本的IoC功能。
 - ApplicationContext：扩展后的IoC。
 - BeanDefinition：Bean对象在IoC中的抽象，Spring以此管理各种对象以及依赖关系。

# BeanFactory

## 简述

 - `getBean` ： 获取IoC容器中管理的Bean
 - `containsBean` ： 获取IoC容器中管理的Bean

## DefaultListableBeanFactory

DefaultListableBeanFactory 默认的功能完整的IoC容器

```java
public class DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory
		implements ConfigurableListableBeanFactory, BeanDefinitionRegistry, Serializable {}
```

 - `AbstractAutowireCapableBeanFactory` 实现属性的自动绑定功能
 - `ConfigurableListableBeanFactory` 提供了对Bean定义的分析和修改的便利方法，同时也提供了对单例的预实例化。
 - `BeanDefinitionRegistry` 提供了BeanDefinition的管理

## ApplicationContext

# IoC容器初始化过程

包括BeanDefinition的Resource定位、载入和注册。
