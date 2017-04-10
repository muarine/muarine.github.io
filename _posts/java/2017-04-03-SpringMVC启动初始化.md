---
layout: post
title: SpringMVC初始化过程
categories: Java
description: SpringMVC初始化过程
---

##掌握SpringMVC启动过程

SpringMVC中web.xml配置

```xml
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

其中ContextLoaderListener作为初始化入口文件，核心方法如下：

```java
public class ContextLoaderListener extends ContextLoader implements ServletContextListener {
	/**

	 */
	public ContextLoaderListener() {
	}
	/**
	 */
	public ContextLoaderListener(WebApplicationContext context) {
		super(context);
	}
	/**
	初始化根context
	 */
	@Override
	public void contextInitialized(ServletContextEvent event) {
		initWebApplicationContext(event.getServletContext());
	}
	/**
	销毁context
	 */
	@Override
	public void contextDestroyed(ServletContextEvent event) {
		closeWebApplicationContext(event.getServletContext());	ContextCleanupListener.cleanupAttributes(event.getServletContext());
	}
```



##参考资料##

[SpringMVC启动过程详解](http://www.cnblogs.com/RunForLove/p/5688731.html)
