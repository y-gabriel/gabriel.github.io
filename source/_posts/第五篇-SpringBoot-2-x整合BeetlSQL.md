---
title: 第五篇-SpringBoot-2-x整合BeetlSQL
date: 2018/10/16
updated: 2018/10/16
---

![image.png](https://upload-images.jianshu.io/upload_images/9988457-8e01e006739b1482.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上图是[BeetlSQL 官网](http://ibeetl.com/guide/#beetlsql)中对 BeetlSQL 的介绍，简单来说我们可以得到几个点

1. 开发效率高
2. 维护性好
3. 性能数倍于 JPA MyBatis

关于 BeetlSQL 的更多介绍大家可以去到官网去看看，接下来我们来看看如何把这个 DAO 工具整合到项目中

pom.xml

```
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>


		<!-- 引入beetlsql -->
		<dependency>
			<groupId>com.ibeetl</groupId>
			<artifactId>beetlsql</artifactId>
			<version>2.10.34</version>
		</dependency>
		<!-- 引入beetl -->
		<dependency>
			<groupId>com.ibeetl</groupId>
			<artifactId>beetl</artifactId>
			<version>2.9.3</version>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
```

sql 文件，我这里用的是 mysql

```sql
CREATE TABLE `test`.`Untitled`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `nickname` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `age` int(11) NULL DEFAULT 18,
  `cdate` timestamp(0) NULL DEFAULT CURRENT_TIMESTAMP(0),
  `udate` timestamp(0) NULL DEFAULT CURRENT_TIMESTAMP(0),
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;
```

User.java

```
package com.priv.gabriel.entity;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-14
 * @Description:
 */
public class User {

	private long id;

	private String username;

	private String nickname;

	private int age;

	public long getId() {
		return id;
	}

	public void setId(long id) {
		this.id = id;
	}

	public String getUsername() {
		return username;
	}

	public void setUsername(String username) {
		this.username = username;
	}

	public String getNickname() {
		return nickname;
	}

	public void setNickname(String nickname) {
		this.nickname = nickname;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	@Override
	public String toString() {
		return "User{" +
				"id=" + id +
				", username='" + username + '\'' +
				", nickname='" + nickname + '\'' +
				", age=" + age +
				'}';
	}
}
```

在这里有两个分支，一种是通过 sqlManager 来操作，另一种是整合 mapper，在这里我们现看看第一种方式

## SQLManager 方式

UserControllerForSQLManager.java

```java
package com.priv.gabriel.controller;

import com.priv.gabriel.entity.User;
import com.priv.gabriel.repository.UserRepository;
import org.beetl.sql.core.SQLManager;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-14
 * @Description:
 */
@RestController
@RequestMapping("/sqlManager/users")
public class UserControllerForSQLManager {

	//自动注入即可
	@Autowired
	private SQLManager sqlManager;

	/*
	 * @Author Gabriel
	 * @Description 根据主键查找记录
	 * @Date 2018/10/16
	 * @Param [id] 主键
	 * @Return void
	 */
	@RequestMapping(value = "/{id}",method = RequestMethod.GET)
	public User selectUserById(@PathVariable("id")int id){
		//如果没有查到数据则抛出异常
		//return sqlManager.unique(User.class,id);
		//如果没有查到数据则返回null
		return sqlManager.single(User.class,id);
	}

	/*
	 * @Author Gabriel
	 * @Description 查询所有
	 * @Date 2018/10/16
	 * @Param []
	 * @Return java.util.List<com.priv.gabriel.entity.User>*/
	@RequestMapping(value = {"","/"},method = RequestMethod.GET)
	public List<User> getUsers(){
		//获取所有数据
		//return sqlManager.all(User.class);
		//查询该表的总数
		//return sqlManager.allCount(User.class);
		//获取所有数据 分页方式
		return sqlManager.all(User.class,1,2);
	}

	/*
	 * @Author Gabriel
	 * @Description 单表条件查询
	 * @Date 2018/10/16
	 * @Param []
	 * @Return void*/
	public void singletonTableQuery(){
		//通过sqlManager.query()可以在后面追加各种条件
		sqlManager.query(User.class).andLike("username","admin").orderBy("age").select();
	}

	/*
	 * @Author Gabriel
	 * @Description 新增数据
	 * @Date 2018/10/16
	 * @Param [user]
	 * @Return void*/
	@RequestMapping(value = {"","/"},method = RequestMethod.POST)
	public void addUser(User user){
		//添加数据到对应表中
		//sqlManager.insert(User.class,user);
		//添加数据到对应表中，并返回自增id
		sqlManager.insertTemplate(user,true);
		System.out.println(user.getId());
		System.out.println("新增成功");
	}

	/*
	 * @Author Gabriel
	 * @Description 根据主键修改
	 * @Date 2018/10/16
	 * @Param [user]
	 * @Return java.lang.String*/
	@RequestMapping(value = {"","/"},method = RequestMethod.PUT)
	public String updateById(User user){
		//根据id修改，所有值都参与更新
		//sqlManager.updateById(user);
		//根据id修改,属性为null的不会更新
		if(sqlManager.updateTemplateById(user)>0){
			return "修改成功";
		}else{
			return "修改失败";
		}
	}

	/*
	 * @Author Gabriel
	 * @Description 删除记录
	 * @Date 2018/10/16
	 * @Param [id]
	 * @Return java.lang.String*/
	@RequestMapping(value = "/id",method = RequestMethod.DELETE)
	public String deleteById(@PathVariable("id") int id){
		if(sqlManager.deleteById(User.class,id) >0 ){
			return "删除成功";
		}else{
			return "删除失败";
		}
	}
}
```

## Mapper 方式

如果要使用 mapper 方式，则需要新建一个 mapper 接口，并继承 BaseMapper<T>
UserRepository.java

```java
package com.priv.gabriel.repository;

import com.priv.gabriel.entity.User;
import org.beetl.sql.core.mapper.BaseMapper;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-14
 * @Description:
 */
public interface UserRepository extends BaseMapper<User>{

}
```

UserControllerForMapper.java

```java
package com.priv.gabriel.controller;

import com.priv.gabriel.entity.User;
import com.priv.gabriel.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-14
 * @Description:
 */
@RestController
@RequestMapping("/mapper/users")
public class UserControllerForMapper {

	@Autowired
	private UserRepository repository;

	@RequestMapping(value = {"","/"},method = RequestMethod.POST)
	public void addUser(User user){
		repository.insert(user);
	}

	@RequestMapping(value = {"","/"},method = RequestMethod.DELETE)
	public String deleteUserById(User user){
		if(repository.deleteById(user) >0 ){
			return "删除成功";
		}else{
			return "删除失败";
		}
	}

	@RequestMapping(value = {"","/"},method = RequestMethod.PUT)
	public String updateUserById(User user){
		//repository.updateById(user)
		if(repository.deleteById(user) > 0){
			return "修改成功";
		}else{
			return "修改失败";
		}
	}

	@RequestMapping(value = "/{id}",method = RequestMethod.GET)
	public User selectUserById(@PathVariable("id")int id){
		//repository.unique(id);
		return repository.single(id);
	}

	@RequestMapping(value = {"","/"},method = RequestMethod.GET)
	public List<User> getUsers(){
		//repository.all(1,2);
		//repository.allCount();
		return repository.all();
	}
}
```

两种方式都介绍完毕了，但是 BeetlSQL 的重点部分还不在这，BeetlSQL 的重点是可以创建一个 SQL 模板，到这大家可能会想，不就是个 xml 嘛，mybatis 就有呀，不一样的地方就在这了，BeetlSQL 的 SQL 模板是这样的

```md
# selectByTest

select \* from user where 1=1
```

怎么样，是不是眼前一亮，很明显 `selectByTest` 是这条 SQL 语句的 id , `===`的作用是代表 id 和内容的分割，而最后的部分当然就是 SQL 语句啦
然后简单介绍一下调用 SQL 模板的方式

## SQLManager 方式

```java
	@RequestMapping(value = "/test",method = RequestMethod.GET)
	public List<User> getUsersByTest(){
		return sqlManager.select("user.selectByTest",User.class);
	}
```

在 SQLManager 的方式中，通过`sqlManager.select("模板id"，类型)`的方式直接调用

## Mapper 的方式

```java
@SqlResource("user")
public interface UserRepository extends BaseMapper<User>{
	List<User> selectByTest();
}
```

在 Mapper 的方式，需要先建立一个`xxx.md`的 SQL 模板文件,通过`@SqlResource(模板文件名)`这个注解找到模板文件，再在 mapper 中写入与模板文件中同名的方法，即可在外部调用
注意，BeetlSQL 的模板文件位置默认在`resource/sql/xxx.md`中，好啦，关于 BeetlSQL 的介绍就到这里。
BeetlSQL 的详细介绍
[Beetl 官方文档](http://ibeetl.com/guide/#beetl)
[BeetlSQL 官方文档](http://ibeetl.com/guide/#beetlsql)
[项目点此下载](https://gitee.com/phoenixs_101/SpringBoot2.x-Learning/tree/master/demoforbeetlsql)
