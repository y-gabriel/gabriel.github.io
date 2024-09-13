---
title: 第九篇-SpringBoot-2-x全局异常处理
date: 2018/10/25
updated: 2018/10/25
---

关于对异常的处理也是我们在开发过程一个比较大的问题，今天我们就来看看 SpringBoot 中如何处理异常。

### TempException.java

```
package com.priv.gabriel.exception;

import lombok.AllArgsConstructor;
import lombok.Data;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-23
 * @Description:
 */
@Data
@AllArgsConstructor
public class TempException extends RuntimeException {

	private String code;

	private String message;
}
```

###### 需要继承 RuntimeException 事物才会进行回滚

### GlobalExceptionHandler.java

```java
package com.priv.gabriel.exception.handler;

import com.priv.gabriel.exception.TempException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-23
 * @Description:
 */
@ControllerAdvice
public class GlobalExceptionHandler {

	@ResponseBody
	@ExceptionHandler(value = TempException.class)
	public TempException tempErrHandler(TempException e){
		TempException exception = new TempException(e.getCode(),e.getMessage());
		return exception;
	}
}
```

### DemoController.java

```java
package com.priv.gabriel.controller;

import com.priv.gabriel.exception.TempException;
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
@RequestMapping("/demo")
public class DemoController {

	@RequestMapping("/index")
	public String index(){
		throw new TempException("111","没找到对象");
	}
}
```

[项目下载地址](https://gitee.com/phoenixs_101/SpringBoot2.x-Learning/tree/master/demoforglobalexception)
