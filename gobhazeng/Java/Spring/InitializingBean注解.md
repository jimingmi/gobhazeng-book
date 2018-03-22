# InitializingBean注解

`InitializingBean`接口为bean提供了初始化方法的方式，它只包括`afterPropertiesSet()`方法，凡是继承该接口的类，在初始化bean的时候会执行该方法。

```java
public interface InitializingBean {

	/**
	 * Invoked by a BeanFactory after it has set all bean properties supplied
	 * (and satisfied BeanFactoryAware and ApplicationContextAware).
	 * <p>This method allows the bean instance to perform initialization only
	 * possible when all bean properties have been set and to throw an
	 * exception in the event of misconfiguration.
	 * @throws Exception in the event of misconfiguration (such
	 * as failure to set an essential property) or if initialization fails.
	 */
	void afterPropertiesSet() throws Exception;

}
```

spring初始化bean的时候，如果该bean是实现了InitializingBean接口，并且同时在配置文件中指定了init-method，系统则是先调用afterPropertiesSet方法，然后在调用init-method中指定的方法。

**通过阅读源码可以知晓其实现的原理：**

```java
/**
 * Give a bean a chance to react now all its properties are set,
 * and a chance to know about its owning bean factory (this object).
 * This means checking whether the bean implements InitializingBean or defines
 * a custom init method, and invoking the necessary callback(s) if it does.
 * @param beanName the bean name in the factory (for debugging purposes)
 * @param bean the new bean instance we may need to initialize
 * @param mbd the merged bean definition that the bean was created with
 * (can also be {@code null}, if given an existing bean instance)
 * @throws Throwable if thrown by init methods or by the invocation process
 * @see #invokeCustomInitMethod
 */
protected void invokeInitMethods(String beanName, final Object bean, @Nullable RootBeanDefinition mbd)
		throws Throwable {

	// 判断bean是否实现了InitializingBean接口
	boolean isInitializingBean = (bean instanceof InitializingBean);
	if (isInitializingBean && (mbd == null || !mbd.isExternallyManagedInitMethod("afterPropertiesSet"))) {
		if (logger.isDebugEnabled()) {
			logger.debug("Invoking afterPropertiesSet() on bean with name '" + beanName + "'");
		}
		if (System.getSecurityManager() != null) {
			try {
				AccessController.doPrivileged((PrivilegedExceptionAction<Object>) () -> {
					((InitializingBean) bean).afterPropertiesSet();
					return null;
				}, getAccessControlContext());
			}
			catch (PrivilegedActionException pae) {
				throw pae.getException();
			}
		}
		else {
			// 执行afterPropertiesSet()方法
			((InitializingBean) bean).afterPropertiesSet();
		}
	}

	if (mbd != null && bean.getClass() != NullBean.class) {
		String initMethodName = mbd.getInitMethodName();
		// 判断是否指定了init-method方法，如果指定了init-method方法，则再调用制定的init-method
		if (StringUtils.hasLength(initMethodName) &&
				!(isInitializingBean && "afterPropertiesSet".equals(initMethodName)) &&
				!mbd.isExternallyManagedInitMethod(initMethodName)) {
			// 通过反射加载init-method方法
			invokeCustomInitMethod(beanName, bean, mbd);
		}
	}
}
```

 - 1. spring为bean提供了两种初始化bean的方式，实现InitializingBean接口，实现afterPropertiesSet方法，或者在配置文件中同过init-method指定，两种方式可以同时使用

 - 2. 实现InitializingBean接口是直接调用afterPropertiesSet方法，比通过反射调用init-method指定的方法效率相对来说要高点。但是init-method方式消除了对spring的依赖

 - 3. 如果调用afterPropertiesSet方法时出错，则不调用init-method指定的方法。
