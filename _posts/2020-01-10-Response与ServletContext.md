---
layout:     post
title:      Response 与 ServletContext
subtitle:   Response 与 ServletContext 基础知识
date:       2020-01-10
author:     TGY
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Java
    - JavaWeb
---
### 1. Response
#### 1.1 设置响应消息
##### 1.1.1 设置响应行
>格式：HTTP/1.1 200 ok

1. 设置状态码
```
void setStatus(int sc)
```
2. 设置响应头
```
setHeader(String name, String value)
```
##### 1.1.2 设置响应体
1. 使用字符输出
```
PrintWriter getWriter()
```
2. 使用字节输出
```
ServletOutputStream getOutputStream()
```
#### 1.2 重定向
当前请求的资源不能处理，能处理的是在当前服务器的其他请求或者其他服务器上。
##### 1.2.1 实现
1. 设置状态码为302
-  302:代表暂时重定向
-  301:代表永久重定向
```
  response.setStatus(302);
```
2.设置响应头location
```
response.setHeader("location","/day/xxxx");
//response.setHeader("location","http://www.baidu.com");
```
简单的实现，把上面两步合成一步
```
response.sendRedirect("/day/xxxx");
//response.sendRedirect("http://www.baidu.com");
```
##### 1.2.2 重定向与转发对比
- 重定向的特点:redirect
1. 地址栏发生变化
2. 重定向可以访问**其他站点(服务器)的资源**
3. 重定向是**两次请求**，所以不能使用request对象来共享数据
- 转发的特点：forward
1. 转发地址栏路径不变
2. 转发只能访问当前服务器下的资源
3. 转发是**一次请求**，可以使用request对象来共享数据
#### 1.3 服务器路径
##### 1.3.1 相对路径
规则：找到当前资源和目标资源之间的相对位置关系
- \* ./：当前目录
- \* ../:后退一级目录

##### 1.3.2 绝对路径
通过绝对路径可以确定唯一资源, 以/开头的路径
##### 1.3.3 路径的写法
**判断请求将来从哪里发出**
1. 给客户端浏览器使用
  需要加虚拟目录(项目的访问路径) ，建议虚拟目录动态获取：
    ```
    request.getContextPath()
    ```
     如:  <a> , <form>,  重定向等。
2. 给服务器使用
不需要加虚拟目录,  如: 消息转发
### 1.4 乱码解决
```
// 设置获取的输出流格式
resp.setContentType("utf-8");
// 告诉浏览器响应体使用的编码
response.setContentType("text/html;charset=utf-8");
```
### 2. ServletContext
#### 2.1 概念
代表整个web应用，可以和程序的容器(服务器)来通信
#### 2.2 获取：
1. 通过request对象获取
```
request.getServletContext();
```
2. 通过HttpServlet获取
```
this.getServletContext();
```

#### 2.3 功能：
##### 2.3.1. 获取MIME类型：
1. MIME类型:在互联网通信过程中定义的一种文件数据类型
			* 格式： 大类型/小类型   text/html		image/jpeg

2. 获取
```
String getMimeType(String file)
```
##### 2.3.2 域对象：共享数据
```
setAttribute(String name,Object value)
getAttribute(String name)
removeAttribute(String name)
```
ServletContext对象范围：所有用户所有请求的数据
##### 2.3.3. 获取文件的真实(服务器)路径
```
String getRealPath(String path)
```
```
//web目录下资源访问
context.getRealPath("/b.txt");
//WEB-INF目录下的资源访问
context.getRealPath("/WEB-INF/c.txt");
//src目录下的资源访问
context.getRealPath("/WEB-INF/classes/a.txt");
```
