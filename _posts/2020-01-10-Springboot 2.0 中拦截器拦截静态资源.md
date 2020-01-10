---
layout:     post
title:      Springboot 2.0 拦截器
subtitle:   Springboot 2.0 中拦截器拦截静态资源
date:       2020-01-10
author:     TGY
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Java
    - Springboot
    - 拦截器
---

>在使用Springboot 2.0以及以上版本中，使用 `HandlerInterceptor` 做登录验证请求时，发现2.0以上版本的拦截器会拦截所有请求，包括静态资源，**这里的静态资源中不仅仅包括css，js，image等，还包括不存在的url。**也即是说springboot在处理没有对应的Controller方法url 时，会把这些url当作静态资源处理。

如下:
```
@Slf4j
public class WebMvcInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        log.info(handler.toString());
        Object user = request.getSession().getAttribute("user");
        System.out.println("WebMvcInterceptor:" + request.getRequestURL());
        if (user == null){
            response.sendRedirect("/tologin");
            return false;
        }
        return true;
    }
}
```
对于 `css` `js`  `image`, 我们可以在注册拦截器的时候排除掉。
```
@Override
    public void addInterceptors(InterceptorRegistry registry) {
        
        registry.addInterceptor(new WebMvcInterceptor())
                .addPathPatterns("/**")
                .excludePathPatterns("/tologin","/login","/error/**","/favicon.ico","/css/**","/images/**","/js/**");
    }
```
但是对于不存在的url，就不能排除了。

这时想到开始说的，springboot对于静态资源是统一处理的。`preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)`,这个方法的 `handler` 在拦截到静态资源时，类型其实是 `ResourceHttpRequestHandler` 类型。这时对于静态资源的排除，在当前方法中把 `ResourceHttpRequestHandler`类型排除掉即可。
```
@Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        log.info(handler.toString());
      //如果 handler是 ResourceHttpRequestHandler，直接放行
        if (handler instanceof ResourceHttpRequestHandler){

            return true;
        }

        Object user = request.getSession().getAttribute("user");
        System.out.println("WebMvcInterceptor:" + request.getRequestURL());
        if (user == null){

            response.sendRedirect("/tologin");
            return false;
        }

        return true;
    }
```
