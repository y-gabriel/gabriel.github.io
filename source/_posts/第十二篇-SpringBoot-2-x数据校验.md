---
title: 第十二篇-SpringBoot-2-x数据校验
date: 2018/11/20
updated: 2018/11/20
---

#介绍
在项目的过程中，对于参数的校验是必须的，如果参数比较少的话我们可以直接通过代码进行校验，但是数据较大时再用这个方法就比较笨重了，接下来就该我们的主角`Validation`闪亮登场了
####pom.xml

```
	<dependencies>
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

实际上在这里数据校验都是调用的`javax.validation`，`spring-boot-starter-web`中也包含了`hibernate-validator`，有兴趣的可以去翻翻文档

### User.java

```java
package com.priv.gabriel.entity;

import lombok.Data;
import org.hibernate.validator.constraints.Length;

import javax.validation.constraints.Min;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;


/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-23
 * @Description:
 */
@Data
public class User {

	@NotBlank(message = "name不允许为空")
	@Length(min = 2,max = 10,message = "你的长度不对劲呀")
	private String name;
	@NotNull(message = "进入未成年人入内！")
	@Min(18)
	private int age;
	@NotBlank(message = "拒绝黑户")
	private String address;

}
```

接下来只需要在`Controller`层中使用`@valid`进行校验就可以了

```java
package com.priv.gabriel.controller;

import com.priv.gabriel.entity.User;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.validation.Valid;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-23
 * @Description:
 */
@RestController
@RequestMapping("/user")
public class UserController {

	@RequestMapping("/check")
	public String check(@Valid User user){
		return "检测完毕！没有问题";
	}
}
```

用`Rest Client`工具简单检测一下
![image.png](https://upload-images.jianshu.io/upload_images/9988457-d46bb655cc1d4505.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
结果当然是...

```json
{
  "timestamp": "2018-10-23T09:01:45.159+0000",
  "status": 400,
  "error": "Bad Request",
  "errors": [
    {
      "codes": [
        "Length.user.name",
        "Length.name",
        "Length.java.lang.String",
        "Length"
      ],
      "arguments": [
        {
          "codes": ["user.name", "name"],
          "arguments": null,
          "defaultMessage": "name",
          "code": "name"
        },
        10,
        2
      ],
      "defaultMessage": "你的长度不对劲呀",
      "objectName": "user",
      "field": "name",
      "rejectedValue": "",
      "bindingFailure": false,
      "code": "Length"
    },
    {
      "codes": ["Min.user.age", "Min.age", "Min.int", "Min"],
      "arguments": [
        {
          "codes": ["user.age", "age"],
          "arguments": null,
          "defaultMessage": "age",
          "code": "age"
        },
        18
      ],
      "defaultMessage": "最小不能小于18",
      "objectName": "user",
      "field": "age",
      "rejectedValue": 17,
      "bindingFailure": false,
      "code": "Min"
    },
    {
      "codes": [
        "NotBlank.user.address",
        "NotBlank.address",
        "NotBlank.java.lang.String",
        "NotBlank"
      ],
      "arguments": [
        {
          "codes": ["user.address", "address"],
          "arguments": null,
          "defaultMessage": "address",
          "code": "address"
        }
      ],
      "defaultMessage": "拒绝黑户",
      "objectName": "user",
      "field": "address",
      "rejectedValue": "",
      "bindingFailure": false,
      "code": "NotBlank"
    },
    {
      "codes": [
        "NotBlank.user.name",
        "NotBlank.name",
        "NotBlank.java.lang.String",
        "NotBlank"
      ],
      "arguments": [
        {
          "codes": ["user.name", "name"],
          "arguments": null,
          "defaultMessage": "name",
          "code": "name"
        }
      ],
      "defaultMessage": "name不允许为空",
      "objectName": "user",
      "field": "name",
      "rejectedValue": "",
      "bindingFailure": false,
      "code": "NotBlank"
    }
  ],
  "message": "Validation failed for object='user'. Error count: 4",
  "path": "/user/check"
}
```

好了，到这里可以看到我们的设置已经生效了，关于数据校验你`get`到了吗？
[项目下载地址](https://gitee.com/phoenixs_101/SpringBoot2.x-Learning/tree/master/demoforvalidation)
