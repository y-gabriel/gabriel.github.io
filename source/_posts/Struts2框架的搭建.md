---
title: Struts2框架的搭建
date: 2018/03/07
updated: 2018/03/07
---

搭建一个 struts2 的框架，在之前已经搭建过 struts 的框架了，这里的流程基本上差不多，详见 [struts1 的搭建](https://www.jianshu.com/p/b0c05b6876f5)

首先到官网上下载 jar 包，这里附一个 git 的链接[struts2jar 包下载](https://github.com/YangLinJun/lib/tree/master/struts)

新建工程，将下载的 jar 解压至工程中，项目结构如下：

![项目结构](http://upload-images.jianshu.io/upload_images/9988457-542a2ee69733a41a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来编写 struts.xml

> 默认加载的配置文件名为 struts.xml
>
> > `private static final String DEFAULT_CONFIGURATION_PATHS = "struts-default.xml,struts-plugin.xml,struts.xml"; `此处为 Dispatcher 中的设置

如果要默认读取的位置需要在 struts2filter 中加入

```
    <init-param>
        <param-name>filterConfig</param-name>
        <param-value>classpath:struts2_demo/struts.xml</param-value>
    </init-param>
```

下面是 struts.xml 的配置

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
    "http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>
	<!-- package是包，是struts提供的对于action的管理方式 -->
	<!-- name是package的唯一标示符 -->
	<!-- extends 继承 继承会将被继承包的action与result都继承到,而所有包都需要继承struts-default这个一个包 -->
	<!-- struts-default这一个包是struts提供给我们的一个默认包,里面包含了很多已经定义好的拦截器与result -->
	<package name="hello" extends="struts-default">
		<!-- action 是指一个具体的动作 -->
		<!-- class 指处理这一具体动作的类 -->
		<!-- method 指处理该动作的类的方法,默认为execute -->
		<action name="hi" class="com.education.action.HelloWorldAction"
			method="execute">
			<!-- result 返回结果 -->
			<!-- name 指执行方法的返回值,默认为success -->
			<!-- result中间的值是最终跳转到的页面 -->
			<!-- result的默认跳转方式为转发,如果需要重定向,则增加一个属性 type="redirect";重定向到某个action type="redirectAction" -->
			<result>/success.jsp</result>
			<result name="error">/fail.jsp</result>
		</action>
	</package>
</struts>
```

struts.xml 配置好后需要在 web.xml 中加入 struts 的过滤器

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" version="3.0">
  <display-name>struts2_demo</display-name>
  <!-- 配置struts2的过滤器 -->
  <filter>
	<filter-name>struts2</filter-name>
	<!-- 该类可在struts2-core包中找到 -->
	<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
  </filter>
  <!-- 过滤器映射 -->
  <filter-mapping>
  	<filter-name>struts2</filter-name>
  	<!-- 拦截所有路径 -->
  	<url-pattern>/*</url-pattern>
  </filter-mapping>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```

struts 配置完毕后建立一个 action

```
package com.education.action;

import com.education.bean.User;
import com.opensymphony.xwork2.ActionSupport;

//action都会继承一个ActionSupport,单这不是强制的,ActionSupport中包含了很多方法以及常用常量
public class HelloWorldActionextends ActionSupport {

	// 前台传入的值会直接注入到该Action的属性中,必须含有get/set方法
	// 如果是非对象则以 <input name="testMsg"/>这样的形式传值
	// 如果是对象则以<input name="user.username"/>这样的形式传值
	private User user;

	private String testMsg;

	public String getTestMsg() {
		return testMsg;
	}

	public void setTestMsg(String testMsg) {
		this.testMsg = testMsg;
	}

	public User getUser() {
		return user;
	}

	public void setUser(User user) {
		this.user = user;
	}

	// execute方法是当struts.xml没有指定方法来处理请求时,就会默认调用该方法
	@Override
	public String execute() throws Exception {
		return SUCCESS;
	}

	// validate方法是struts框架自带的验证方法
	// 如果重写了该方法则会先于execute方法执行
	// 如果运行了addFieldError方法则会直接返回,不再执行execute方法
	// 返回值为input
	@Override
	public void validate() {
		if (1 > 2) {
			addFieldError("name", "it's impossible");
		}
	}
}
```

好了，一个 struts2 框架的项目就已经搭建完毕了,这里就不再发 jsp 页面的代码了，剩下的部分就请自己补全吧。
这里附上完整项目的下载链接
[点此下载](https://github.com/YangLinJun/lib/blob/master/struts/struts2_demo.rar)
