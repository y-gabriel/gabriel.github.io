---
title: 第六篇-SpringBoot-2-x添加Druid作为数据库连接池
date: 2018/10/17
updated: 2018/10/17
---

整合了一大堆 ORM，是时候增加一个连接池了，此处选用了 druid 作为连接池，druid 是 alibaba 开源平台上的一个数据库连接池实现，对比 c3p0，dbcp 加入了对数据库的监控，不知道甩出几条街的距离，个人推为数据库连接池的首选(手动摊手)
这里仍然使用 jpa+druid
首先先来看看引入的依赖

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
		<!-- 引入druid依赖 -->
		<!-- 此处的引用有两种 -->
		<!-- 一种是直接引用druid包,另一种是引用starter方式 -->
		<!-- 在此处我引用的是start包,为什么呢，因为方便呀... -->
		<!-- 注:如果没有该包,application.properties/application.yml 中将不会出现关于druid的提示 -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid-spring-boot-starter</artifactId>
			<version>1.1.10</version>
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

application.properties

```properties
#jdbc配置
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql:///test
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource

#连接池的设置
#初始化时建立物理连接的个数
spring.datasource.druid.initial-size=5
#最小连接池数量
spring.datasource.druid.min-idle=5
#最大连接池数量 maxIdle已经不再使用
spring.datasource.druid.max-active=20
#获取连接时最大等待时间，单位毫秒
spring.datasource.druid.max-wait=60000
#申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。
spring.datasource.druid.test-while-idle=true
#既作为检测的间隔时间又作为testWhileIdel执行的依据
spring.datasource.druid.time-between-eviction-runs-millis=60000
#销毁线程时检测当前连接的最后活动时间和当前时间差大于该值时，关闭当前连接
spring.datasource.druid.min-evictable-idle-time-millis=30000
#用来检测连接是否有效的sql 必须是一个查询语句
#mysql中为 select 'x'
#oracle中为 select 1 from dual
spring.datasource.druid.validation-query=select 'x'
#申请连接时会执行validationQuery检测连接是否有效,开启会降低性能,默认为true
spring.datasource.druid.test-on-borrow=false
#归还连接时会执行validationQuery检测连接是否有效,开启会降低性能,默认为true
spring.datasource.druid.test-on-return=false
#当数据库抛出不可恢复的异常时,抛弃该连接
spring.datasource.druid.exception-sorter=true
#是否缓存preparedStatement,mysql5.5+建议开启
#spring.datasource.druid.pool-prepared-statements=true
#当值大于0时poolPreparedStatements会自动修改为true
spring.datasource.druid.max-pool-prepared-statement-per-connection-size=20
#配置扩展插件
spring.datasource.druid.filters=stat,wall
#通过connectProperties属性来打开mergeSql功能；慢SQL记录
spring.datasource.druid.connection-properties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
#合并多个DruidDataSource的监控数据
spring.datasource.druid.use-global-data-source-stat=true
#设置访问druid监控页的账号和密码,默认没有
#spring.datasource.druid.stat-view-servlet.login-username=admin
#spring.datasource.druid.stat-view-servlet.login-password=admin

#jpa设置
spring.jpa.database=mysql
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=update
```

关于 jpa 的搭建的话，建议查看这个[SpringBoot 整合 JPA](https://www.jianshu.com/p/62e499849a73)
如果是按照我这种方式的话，那么现在就已经整合完毕了
接来下访问一下 druid 的监控页[druid](http://localhost:8080/druid)
![image.png](https://upload-images.jianshu.io/upload_images/9988457-d8132827aa5cf67a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
再看看连接池的配置成功没有
![image.png](https://upload-images.jianshu.io/upload_images/9988457-dcbe05ad5bb12cbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
以及 sql 的监控
![image.png](https://upload-images.jianshu.io/upload_images/9988457-b0333745a1374cd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还记得上面说的 druid 的两种配置方式吗，上一种的话，只需要我们写好配置文件就 OK 了，而下一种就麻烦一点，我在这里还是把代码贴上

```
package com.priv.gabriel;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.support.http.StatViewServlet;
import com.alibaba.druid.support.http.WebStatFilter;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;


/**
 * Created with Intellij IDEA.
 *
 * @Author: Gabriel
 * @Date: 2018-10-10
 * @Desciption:
 */

@Configuration

public class DruidConfig {

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource druid(){
        return new DruidDataSource();
    }

    @Bean
    public ServletRegistrationBean druidServlet(){
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(),"/druid/*");
        bean.addInitParameter("allow","127.0.0.1");
        bean.addInitParameter("resetEnable","false");
        bean.addInitParameter("loginUsername","admin");
        bean.addInitParameter("loginPassword","admin");
        return bean;
    }

    @Bean
    public FilterRegistrationBean statFilter(){
        FilterRegistrationBean bean = new FilterRegistrationBean(new WebStatFilter());
        bean.addUrlPatterns("/*");
        bean.addInitParameter("exclusions","*.js,*.gif,/druid/*");
        return bean;
    }
}

```

这种方式需要我们手动的写上一个 servlet 以及 filter，*注意:*这里必须要手动的注入一个 DruidDataSource 而且指定去读取文件，不然它是不会初始化数据源的 [这么个问题搞了半个多小时]

好了，现在 druid 的已经整合完毕啦。
