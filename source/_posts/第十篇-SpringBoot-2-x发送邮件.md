---
title: 第十篇-SpringBoot-2-x发送邮件
date: 2018/10/28
updated: 2018/10/28
---

相信大家之前都写过发送邮件的例子，还记得被密密麻麻的代码包围的恐惧吗？今天介绍一下 SpringBootMail 来发送邮件，体验五六行代码就完成功能的快感！

### pom.xml

```
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-mail</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
```

接着创建一个邮件发送类

### MailController.java

```java
package com.priv.gabriel.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-23
 * @Description:
 */
@RestController
public class MailController {

	@Autowired
	JavaMailSender jms;

	@RequestMapping("/send")
	public String send(){
		SimpleMailMessage message = new SimpleMailMessage();
		message.setFrom("814180366@qq.com");
		message.setTo("youarephoenix@163.com");
		message.setSubject("我来自SpringBoot");
		message.setText("这是一封测试邮件，感谢您的查看");
		jms.send(message);
		return "发送成功!";
	}
}
```

怎么样，就是简单设置了一下发送者和接受者以及邮件的内容，当然了还需要在配置文件中设置一下发送者的信息

### application.properties

```properties
spring.mail.default-encoding=utf-8
spring.mail.host=smtp.qq.com #qq邮箱的smtp地址，如果是其他邮箱则切换成对应的stmp地址
spring.mail.password=xxxxxxxx #如果是qq邮箱则需要把这个切换授权码
spring.mail.port=25
spring.mail.protocol=smtp
spring.mail.username=gabriel
```

最后访问一下
![1.png](https://upload-images.jianshu.io/upload_images/9988457-e8c9d9905635d7b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

总结一下，发送邮件在整合了 mail 之后的 springboot 中已经变成相当简单的一件事情了，但在邮箱的选取上最好还是避免使用 QQ 邮箱，用其他邮箱发送能避免相当多的问题

[项目下载地址](https://gitee.com/phoenixs_101/SpringBoot2.x-Learning/tree/master/demoforjavamail)
