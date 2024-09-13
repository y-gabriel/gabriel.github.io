---
title: 第三篇-SpringBoot-2-x中使用JPA
date: 2018/10/10
updated: 2018/10/10
---

上一篇使用了 JdbcTemplate 去访问数据库，毕竟使用的是原生的 SQL 形式，像我这种懒人是肯定不会考虑的了。。
这次记录下使用 JPA 来极大的减少我们的代码量
首先，还是准备好 SQL 文件

```sql
DROP TABLE IF EXISTS users;

CREATE TABLE users (
id INT ( 11 ) PRIMARY KEY AUTO_INCREMENT,
username VARCHAR ( 255 ) NOT NULL,
passwd VARCHAR ( 255 )
) ENGINE = INNODB DEFAULT CHARSET = utf8;

INSERT users VALUES ( NULL, '翠花', '123' );
INSERT users VALUES ( NULL, '王卫国', '123' );
INSERT users VALUES ( NULL, '李小花', '123' );
INSERT users VALUES ( NULL, '王二柱', '123' );
INSERT users VALUES ( NULL, '赵铁蛋', '123' );
```

这次需要用到的依赖

```xml
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
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

### User.java

```java
package com.priv.gabriel.entity;

import javax.persistence.*;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-08
 * @Desciption:
 */
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;

    @Column(nullable = false)
    private String username;

    @Column(nullable = false)
    private String passwd;

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

    public String getPasswd() {
        return passwd;
    }

    public void setPasswd(String passwd) {
        this.passwd = passwd;
    }
}

```

### UserController.java

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
 * Created by Administrator on 2018/10/9.
 */
@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserRepository userRepository;

    @RequestMapping(value = "/",method = RequestMethod.GET)
    public List<User> usersList(){
        return userRepository.findAll();
    }

    @RequestMapping(value = "/" ,method = RequestMethod.PUT)
    public String updateUser(User user){
        if(userRepository.save(user) != null){
            return "修改成功";
        }else{
            return "修改失败";
        }
    }

    @RequestMapping(value = "/{id}",method = RequestMethod.GET)
    public User selectUserById(@PathVariable long id){
        return userRepository.findById(id).get();
    }

    @RequestMapping(value ="/{id}",method = RequestMethod.DELETE)
    public String deleteUser(@PathVariable long id){
        userRepository.deleteById(id);
        return "删除成功";
    }

    @RequestMapping(value = "/",method = RequestMethod.POST)
    public String saveUser(User user){
        System.out.println(userRepository);
        if(userRepository.save(user) != null){
            return "新增成功";
        }else{
            return "新增失败";
        }
    }
}

```

此处就偷个懒不写 service 层了，要研究的小朋友还是不要学我哈

### UserRepository.java

```java
package com.priv.gabriel.repository;

import com.priv.gabriel.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-08
 * @Desciption:
 */
@Repository
public interface UserRepository extends JpaRepository<User,Long>{
}

```

使用 jpa 最大的好处就是你只需要基础一个 JpaRepository 接口，其余的都交给 jpa 自己去处理，我们只负责调用就好了，回到 springboot 的主题就是

> just run

###2019/01/27 更新
之前介绍的话可能满足不了日常的使用。
今天更新一下 JPA 的其他用法

#自定义方法
