---
title: 第七篇-SpringBoot-2-x集成Lombok
date: 2018/10/18
updated: 2018/10/18
---

之前写了一大堆代码，手都写软了，突然发现我们之前写的代码是这样的

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

这样的

```
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

和这样的

```
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

看出问题了吗，三十行的代码，二十行都在`get/set`还有几行的`toString`，好好的一个实体类，除了属性基本就没什么可看的了，虽然都是靠快捷键生成的，但是有没有什么办法可以让我们少在`get/set方法`上浪费时间呢？
接下来介绍一下本次的主角 Lombok，一个让你可以早点回家陪老婆孩子的神器
先说一下如何使用
pom.xml

```properties
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
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

如果只是引用了的话还不行,需要在 Plugins 里下载一个关于 Lombok 的插件
然后来看看使用过后的实体类

```
package com.priv.gabriel.entity;

import lombok.*;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-14
 * @Description:
 */
@Getter
@Setter
@ToString
@EqualsAndHashCode
public class User {

	private int id;

	private String username;

	private int age;
}
```

甚至是这样的

```
package com.priv.gabriel.entity;

import lombok.*;

/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-14
 * @Description:
 */
@Data
public class User {

	private int id;

	private String username;

	private int age;

}
```

怎么样，感觉到工具类的方便之处了吗，然后简单的介绍一下这里一些注解的含义
`@Setter` 为该类的属性提供 set 方法
`@Getter` 为该类的属性提供 get 方法
`@ToString` 提供 toString 方法
`@EqualsAndHashCode` 提供 equals 和 hashCode 方法
`@NoArgsConstructor` 无参构造
`@AllArgsConstructor` 全参构造
`@RequiredArgsConstructor` 制定参数构造
`@Cleanup` 注解需要放在流的声明上，再也不用因为忘记`finally/try/catch`而烦恼了
`@Data` 大哥，相当于`@ToString,@EqualsAndHashCode,Getter`以及所有非`final`字段的`@Setter,@RequiredArgsConstructor`
`@Builder` 建造者模式
