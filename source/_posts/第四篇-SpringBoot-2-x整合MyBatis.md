---
title: 第四篇-SpringBoot-2-x整合MyBatis
date: 2018/10/14
updated: 2018/10/14
---

用完 spring-data-jpa 之后并不是很想用 mybatis,但是没办法呀，大环境还是 mybatis，而且现在 mybatis 也出了不少插件，我们还是一起看看如何整合 mybatis 吧
关于整合 mybatis 有两种方式，一种是注解方式，另一种是传统的 xml 方式

# 注解方式

先看看引入的依赖

```
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.2</version>
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

sql 文件

```sql
CREATE TABLE `test`.`Untitled`  (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `passwd` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB AUTO_INCREMENT = 4 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;
```

application.properties

```properties
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql:///test?useSSL=true
spring.datasource.username=root
spring.datasource.password=root

#打印sql语句到控制台
logging.level.com.priv.gabriel.demoformybatis.mapper=debug

```

User.java

```java
package com.priv.gabriel.demoformybatis.entity;

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

UserMapper.java

```java
package com.priv.gabriel.demoformybatis.mapper;

import com.priv.gabriel.demoformybatis.entity.User;
import org.apache.ibatis.annotations.*;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-14
 * @Description:
 */
@Mapper
public interface UserMapper {

    /*注解方式的话直接在方法上写上对应的sql语句就可以了*/
    @Select("select * from user where id = #{id}")
    /*如果需要重复使用该Result,给该Results加一个 id 属性，下面即可使用@ResultMap(id)重复调用*/
    @Results(id = "user",value = {
            @Result(property = "username",column = "name")
    })
    User findById(long id);

    /*获取回传自增id*/
    /*id会自动存入user中*/
    @Insert("insert into user(name,passwd) values (#{username},#{passwd})")
    @Options(useGeneratedKeys = true,keyProperty = "id")
    int inertUser(User user);
}

```

UserController.java

```java
package com.priv.gabriel.demoformybatis.controller;

import com.priv.gabriel.demoformybatis.entity.User;
import com.priv.gabriel.demoformybatis.mapper.UserMapper;
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
@RequestMapping("/users")
public class UserController {
    @Autowired
    private UserMapper userMapper;

    @RequestMapping(value = "/{id}",method = RequestMethod.GET)
    public User selectById(@PathVariable long id){
        return userMapper.findById(id);
    }

    @RequestMapping(value = {""},method = RequestMethod.POST)
    public String addUser(User user){
        userMapper.inertUser(user);
        return "现在自增id为"+user.getId();
    }
}

```

在 SpringBoot1.x 中还需要在启动类中加入`@MapperScan("mapper所在目录")`,而在 2.x 版本中不需要加入就可以自动扫描到 mapper 了

## 加入分页

需要引入 pageHelper 依赖

```
        <!-- 引入分页插件 -->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.2.5</version>
        </dependency>
```

在 UserMapper 中新增一个查询所有信息的方法

```
    @Select("select * from user")
    /*调用之前的Results*/
    @ResultMap("user")
    List<User> listUser();
```

在 Controller 中调用

```java
    /*获取分页数据 包含分页详细信息*/
    @RequestMapping(value = "/",method = RequestMethod.GET)
    public PageInfo getAll(@RequestParam Integer page, @RequestParam Integer size){
        PageHelper.startPage(page,size);
        List<User> users = userMapper.listUser();
        PageInfo<User> pageInfo = new PageInfo(users);
        return pageInfo;
    }

    /*仅获取分页数据
    @RequestMapping(value = "/",method = RequestMethod.GET)
    public List<User> getAll(@RequestParam Integer page, @RequestParam Integer size){
        PageHelper.startPage(page,size);
        List<User> users = userMapper.listUser();
        return users;
    }*/
```

# XML 方式

xml 方式的话和以前 Spring 整合 mybatis 没有太大的区别，在这里可以使用 Mybatis-Generator 自动生成代码少些一点代码，其余的话就不多介绍了

## 整合通用 Mapper

不管是使用注解方式还是 XML 方式都还是会存在一个什么都得自己动手的情况，接下来的话，我们整合一个通用的 Mapper，在 mybatis 中体验 jpa 的感觉
引用依赖

```
<!-- 引入通用mapper -->
        <dependency>
            <groupId>tk.mybatis</groupId>
            <artifactId>mapper-spring-boot-starter</artifactId>
            <version>2.0.4</version>
        </dependency>
```

继承 BaseMapper

```java
package com.priv.gabriel.demoformybatis.mapper;


import com.priv.gabriel.demoformybatis.entity.User;
import org.apache.ibatis.annotations.Mapper;
import tk.mybatis.mapper.common.BaseMapper;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-14
 * @Description:
 */
@Mapper
public interface UserCommonMapper extends BaseMapper<User> {
}

```

接着在 Controller 中调用

```java
   @Autowired
    private UserCommonMapper mapper;

    @RequestMapping("/testCommonMapperForSelectAll")
    public List<User> testCommonMapper(){
        return mapper.selectAll();
    }
```

关于 mybatis 的扩展很多，还有像 mybatis-plus，关于这个话有时间再说吧，ε=ε=ε=┏(゜ロ゜;)┛
