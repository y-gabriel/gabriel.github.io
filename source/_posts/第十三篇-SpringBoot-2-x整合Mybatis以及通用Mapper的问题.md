---
title: 第十三篇-SpringBoot-2-x整合Mybatis以及通用Mapper的问题
date: 2018/11/21
updated: 2018/11/21
---

今天听说在 SpringBoot 整合 mybatis 和通用 mapper 的时候会产生一个奇怪的问题，即执行 sql 语句的时候会找不到主键，比如下面这个样子

```
//这是我要执行的方法，很明显就只是查询user表中的所有数据
userMapper.selectAll();

//结果是这样的
2018-11-20 16:17:54.111 DEBUG 10640 --- [nio-8111-exec-1] p.gabriel.mapper.UserMapper.selectAll    : ==>  Preparing: SELECT username,password FROM user
2018-11-20 16:17:54.111 DEBUG 10640 --- [nio-8111-exec-1] p.gabriel.mapper.UserMapper.selectAll    : ==> Parameters:
2018-11-20 16:17:54.129 DEBUG 10640 --- [nio-8111-exec-1] p.gabriel.mapper.UserMapper.selectAll    : <==      Total: 1
```

明显看到 id 这个列并没有被查询到，第一反应是先去查看`User`类

```
package priv.gabriel.model;

import javax.persistence.*;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-11-20
 * @Description:
 */
@Entity
@Table
public class User {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;

	@Column
	private String username;

	@Column
	private String password;

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	@Override
	public String toString() {
		return "User{" +
				"id=" + id +
				", username='" + username + '\'' +
				", password='" + password + '\'' +
				'}';
	}
}
```

然而并没有什么问题，该标识的都标识了，然后又去查了一波`dao`层

```java
package priv.gabriel.mapper;

import org.apache.ibatis.annotations.Mapper;
import priv.gabriel.model.User;
import tk.mybatis.mapper.common.BaseMapper;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-11-20
 * @Description:
 */
@Mapper
public interface UserMapper extends BaseMapper<User>{

}
```

···根本不可能出现问题嘛，然后绞尽脑汁的考虑的各方面的问题，怀疑是版本问题甚至把`tk.mapper`的 2.x 版本挨个下载完了，但是，并没有什么用。
就在我怀疑人生的时候，突然瞟了一眼`User`类，灵光一闪，改造成了如下的款式

```
package priv.gabriel.model;

import javax.persistence.*;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-11-20
 * @Description:
 */
@Entity
@Table
public class User {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	@Column
	private String username;

	@Column
	private String password;

	public Long getId() {
		return id;
	}

	public void setId(Long id) {
		this.id = id;
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	@Override
	public String toString() {
		return "User{" +
				"id=" + id +
				", username='" + username + '\'' +
				", password='" + password + '\'' +
				'}';
	}
}
```

看得出来区别吗？在这里我仅仅是把`id` 从 `int`类型 转为了 `Long`类型居然就好了？？？

```
2018-11-20 16:33:10.236 DEBUG 10076 --- [nio-8111-exec-1] p.gabriel.mapper.UserMapper.selectAll    : ==>  Preparing: SELECT id,username,password FROM user
2018-11-20 16:33:10.257 DEBUG 10076 --- [nio-8111-exec-1] p.gabriel.mapper.UserMapper.selectAll    : ==> Parameters:
2018-11-20 16:33:10.281 DEBUG 10076 --- [nio-8111-exec-1] p.gabriel.mapper.UserMapper.selectAll    : <==      Total: 1
```

。。。
![timg.jpg](https://upload-images.jianshu.io/upload_images/9988457-0a1da1a6c9c60659.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
具体情况还不清楚，我已经准备潜伏到`tk.mapper`的群里问个明白，等搞清楚了再更新

#更新 2018.11.21
在 github 的文档上找到了

> 默认情况下，简单类型会被识别为表中的字段，**但是**，**不 包 含 `Java` 的 基 本 类 型**

![timg (1).jpg](https://upload-images.jianshu.io/upload_images/9988457-71039c28e0a9a519.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

继续翻翻文档，发现下面有一项配置可以支持基本类型
`usePrimitiveType=true`

#总结
虽然之后找到了解决方案，但同时也发现了对应的问题

> 使用基本数据类型就会有默认值，而在`Mybatis`中经常会判断属性值是否为空，为了避免这样的问题最好在类中声明属性时都使用包装类
