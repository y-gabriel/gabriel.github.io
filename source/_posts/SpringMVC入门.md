---
title: SpringMVC入门
date: 2018/11/22
updated: 2018/11/22
---

![执行流程图.png](https://upload-images.jianshu.io/upload_images/9988457-76e0e31883918f34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#Dispathcer
前端控制器，所有请求都需要通过它来进行统一分发

#HandlerMapping
根据 URL 请求寻找对应的标识有`@Controller`的具体处理类

#HandlerAdapter
根据 Handler 来找到支持它的 HandlerAdapter，通过 HandlerAdapter 执行这个 Handler 得到 ModelAndView 对象

#Handler
对 Controller 的 Bean 本身和请求 Method 的包装

#ViewResolver
把逻辑上的视图名称解析为一个真正的视图返还给客户端

#View
即视图本身
