---
title: 第二篇---SpringBoot-2-x中使用JdbcTemplate
date: 2018/10/09
updated: 2018/10/09
---

数据文件

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

需要引入的依赖

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
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

### application.properties

```properties
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql///test
spring.datasource.username=root
spring.datasource.password=root
```

### application.yml

```yml
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql///test
    username: root
    password: root
```

### User.java

```java
package com.prvi.gabriel.springbootforjdbctemplate.entity;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-08
 * @Desciption:
 */
public class User {

    private long id;

    private String username;

    private String password;

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

### UserController.java

```java
package com.prvi.gabriel.springbootforjdbctemplate.controller;

import com.prvi.gabriel.springbootforjdbctemplate.entity.User;
import com.prvi.gabriel.springbootforjdbctemplate.service.UserService;
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
 * @Date: 2018-10-08
 * @Desciption:
 */
@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @RequestMapping(name = "/",method = RequestMethod.GET)
    public List<User> usersList(){
        List<User> users = null;
        return userService.findUsers();
    }

    @RequestMapping(value = "/{id}",method = RequestMethod.GET)
    public User getUserById(@PathVariable long id){
        return userService.findUserById(id);
    }

    @RequestMapping(name = "/",method = RequestMethod.POST,produces = "text/plain;charset=utf-8")
    public String addUser(User user){
        System.out.println(user);
        if(userService.saveUser(user) > 0 ){
            return "新增成功";
        }else{
            return "新增失败";
        }
    }

    @RequestMapping(name = "/",method = RequestMethod.PUT)
    public String updateUserById(User user){
        if(userService.updateUser(user) > 0 ){
            return "修改成功";
        }else{
            return "修改失败";
        }
    }

    @RequestMapping(value = "/{id}",method = RequestMethod.DELETE)
    public String deleteUserById(@PathVariable long id){
        if(userService.delUserById(id) > 0 ){
            return "删除成功";
        }else{
            return "删除失败";
        }
    }
}
```

### UserService.java

```java
package com.prvi.gabriel.springbootforjdbctemplate.service;

import com.prvi.gabriel.springbootforjdbctemplate.entity.User;

import java.util.List;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-08
 * @Desciption:
 */
public interface UserService {

    List<User> findUsers();

    User findUserById(long id);

    int saveUser(User user);

    int delUserById(long id);

    int updateUser(User user);

}
```

### UserServiceImpl.java

```
package com.prvi.gabriel.springbootforjdbctemplate.service;

import com.prvi.gabriel.springbootforjdbctemplate.entity.User;
import com.prvi.gabriel.springbootforjdbctemplate.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-08
 * @Desciption:
 */
@Service
public class UserServiceImpl implements UserService {


    @Autowired
    private UserRepository repository;

    @Transactional(readOnly = true)
    @Override
    public List<User> findUsers() {
        return repository.findUsers();
    }

    @Transactional(readOnly = true)
    @Override
    public User findUserById(long id) {
        return repository.findUserById(id);
    }

    @Transactional
    @Override
    public int saveUser(User user) {
        return repository.saveUser(user);
    }

    @Transactional
    @Override
    public int delUserById(long id) {
        return repository.delUserById(id);
    }

    @Transactional
    @Override
    public int updateUser(User user) {
        return repository.updateUser(user);
    }
}
```

### UserRepository.java

```java
package com.prvi.gabriel.springbootforjdbctemplate.repository;

import com.prvi.gabriel.springbootforjdbctemplate.entity.User;

import java.util.List;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-08
 * @Desciption:
 */
public interface UserRepository {

    List<User> findUsers();

    User findUserById(long id);

    int saveUser(User user);

    int delUserById(long id);

    int updateUser(User user);
}

```

### UserRepositoryImpl.java

```java
package com.prvi.gabriel.springbootforjdbctemplate.repository;

import com.prvi.gabriel.springbootforjdbctemplate.entity.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.PreparedStatementCreator;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.stereotype.Repository;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-08
 * @Desciption:
 */
@Repository
public class UserRepositoryImpl implements UserRepository {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public List<User> findUsers() {
        return jdbcTemplate.query("select * from users",new UserMapper());
    }

    @Override
    public User findUserById(long id) {
        List<User> users = jdbcTemplate.query("select * from users where id = ?",new Object[]{id},new UserMapper());
        User user = null;
        if(users != null&&!users.isEmpty()){
            user = users.get(0);
        }
        return user;
    }

    @Override
    public int saveUser(User user) {
        return jdbcTemplate.update("insert into users (username,password) values (?,?)",new Object[]{user.getUsername(),user.getPassword()});
    }

    @Override
    public int delUserById(long id) {
        return jdbcTemplate.update("delete from users where id = ?",new Object[]{id});
    }

    @Override
    public int updateUser(User user) {
        return jdbcTemplate.update("update users set username = ? , password = ? where id = ?",new Object[]{user.getUsername(),user.getPassword(),user.getId()});
    }
}

class UserMapper implements RowMapper<User>{

    @Override
    public User mapRow(ResultSet resultSet, int i) throws SQLException {
        User user = new User();
        user.setId(resultSet.getLong("id"));
        user.setUsername(resultSet.getString("username"));
        user.setPassword(resultSet.getString("password"));
        return user;
    }
}
```

### UserControllerTest.java

```java
package com.prvi.gabriel.springbootforjdbctemplate.controller;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-08
 * @Desciption:
 */
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class UserControllerTest {

    @Autowired
    private WebApplicationContext context;
    private MockMvc mockMvc;

    @Before
    public void setUp(){
        mockMvc = MockMvcBuilders.webAppContextSetup(context).build();
    }
    @Test
    public void usersList() throws Exception {
        mockMvc.perform(MockMvcRequestBuilders.get("/users"));
    }

    @Test
    public void getUserById() throws Exception {
        mockMvc.perform(MockMvcRequestBuilders.get("/users/1"));
    }

    @Test
    public void addUser() throws Exception {
        mockMvc.perform(MockMvcRequestBuilders.post("/users").param("username","脚后跟").param("password","123"));
    }

    @Test
    public void updateUserById() throws Exception {
        mockMvc.perform(MockMvcRequestBuilders.put("/users").param("id","1").param("username","李海军").param("password","456"));
    }

    @Test
    public void deleteUserById() throws Exception {
        mockMvc.perform(MockMvcRequestBuilders.delete("/users/3"));
    }
}
```
