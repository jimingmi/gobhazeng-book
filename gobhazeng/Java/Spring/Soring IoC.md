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

