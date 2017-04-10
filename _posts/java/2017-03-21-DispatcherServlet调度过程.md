---
layout: post
title: SpringMVC DispatcherServlet调度过程
categories: SpringMVC
description: SpringMVC DispatcherServlet调度过程
---

## 请求响应DispatcherServlet处理流程
### 总结应该如何看源码：
```wiki
1. 根据Servlet生命周期(init/service/destroy) 从顶层往下逐个深入，Servlet-HttpServletBean-FrameworkServlet-DispatcherServlet

2. 根据具体RequestMapper接口服务地址，开启debug模式，把断点放置doService方法内，跟踪代码一步步深入理解从请求到响应，DispatcherServlet的调度原理
```

### 大概的调用流程
1. 获得HandlerMapping
2. 获得HandlerExecutionChain
3. 获得HandleAdapter适配器
4. 检测并注册Handler的拦截器
6. 请求参数绑定
7. 初始化视图容器
8. handle反射调用Controller method
9. 装载ModelAndView
10. 适配ModelAndView
```java
// 开始调度
protected DispatcherServlet.doDispatch(HttpServletRequest request, HttpServletResponse response)

// 遍历已初始化的 请求处理器集合(List<HandlerMapping> handlerMappings） 得到一个Handler执行器
// RequestMappingHandleMapping  SimpleUrlHandleMapping  BeanNameUrlHandleMapping
// 加载context.getBean()获取 HandlerExecutionChain
protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception

// 到这就已经确定该调用哪个Controller.method以及return类型
// HandlerExecutionChain mappedHandler执行链，获取HandleAdapter适配器
HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());


// Apply preHandle methods of registered interceptors. 检测并注册Handler的拦截器
boolean applyPreHandle(HttpServletRequest request, HttpServletResponse response)

// 开始调用Controller.method
ModelAndView mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
// 下面是真正开始调用handler的过程
  /**
	 * Invoke the {@link RequestMapping} handler method preparing a {@link ModelAndView}
	 * if view resolution is required.
	 * @since 4.2
	 * @see #createInvocableHandlerMethod(HandlerMethod)
	 */
	protected ModelAndView invokeHandlerMethod(HttpServletRequest request,
			HttpServletResponse response, HandlerMethod handlerMethod) throws Exception {

		ServletWebRequest webRequest = new ServletWebRequest(request, response);
		try {
      // 请求参数绑定
			WebDataBinderFactory binderFactory = getDataBinderFactory(handlerMethod);
			ModelFactory modelFactory = getModelFactory(handlerMethod, binderFactory);
      // 初始化 反射调用handler
			ServletInvocableHandlerMethod invocableMethod = createInvocableHandlerMethod(handlerMethod);
			invocableMethod.setHandlerMethodArgumentResolvers(this.argumentResolvers);
			invocableMethod.setHandlerMethodReturnValueHandlers(this.returnValueHandlers);
			invocableMethod.setDataBinderFactory(binderFactory);
			invocableMethod.setParameterNameDiscoverer(this.parameterNameDiscoverer);
      // 初始化 视图容器
			ModelAndViewContainer mavContainer = new ModelAndViewContainer();
			mavContainer.addAllAttributes(RequestContextUtils.getInputFlashMap(request));
			modelFactory.initModel(webRequest, mavContainer, invocableMethod);
			mavContainer.setIgnoreDefaultModelOnRedirect(this.ignoreDefaultModelOnRedirect);

			AsyncWebRequest asyncWebRequest = WebAsyncUtils.createAsyncWebRequest(request, response);
			asyncWebRequest.setTimeout(this.asyncRequestTimeout);

			WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);
			asyncManager.setTaskExecutor(this.taskExecutor);
			asyncManager.setAsyncWebRequest(asyncWebRequest);
			asyncManager.registerCallableInterceptors(this.callableInterceptors);
			asyncManager.registerDeferredResultInterceptors(this.deferredResultInterceptors);

			if (asyncManager.hasConcurrentResult()) {
				Object result = asyncManager.getConcurrentResult();
				mavContainer = (ModelAndViewContainer) asyncManager.getConcurrentResultContext()[0];
				asyncManager.clearConcurrentResult();
				if (logger.isDebugEnabled()) {
					logger.debug("Found concurrent result value [" + result + "]");
				}
				invocableMethod = invocableMethod.wrapConcurrentResult(result);
			}
      // 反射invoke执行Controller里的method，并把结果装载进mavContainer
			invocableMethod.invokeAndHandle(webRequest, mavContainer);
			if (asyncManager.isConcurrentHandlingStarted()) {
				return null;
			}
      // 匹配合适的ModelAndView
			return getModelAndView(mavContainer, modelFactory, webRequest);
		}
		finally {
			webRequest.requestCompleted();
		}
	}
// 匹配ModelAndView视图
private ModelAndView getModelAndView(ModelAndViewContainer mavContainer,
			ModelFactory modelFactory, NativeWebRequest webRequest) throws Exception {

		modelFactory.updateModel(webRequest, mavContainer);
    // 对于Restful API 直接return null
		if (mavContainer.isRequestHandled()) {
			return null;
		}
		ModelMap model = mavContainer.getModel();
		ModelAndView mav = new ModelAndView(mavContainer.getViewName(), model, mavContainer.getStatus());
		if (!mavContainer.isViewReference()) {
			mav.setView((View) mavContainer.getView());
		}
		if (model instanceof RedirectAttributes) {
			Map<String, ?> flashAttributes = ((RedirectAttributes) model).getFlashAttributes();
			HttpServletRequest request = webRequest.getNativeRequest(HttpServletRequest.class);
			RequestContextUtils.getOutputFlashMap(request).putAll(flashAttributes);
		}
		return mav;
	}

//
//
```
