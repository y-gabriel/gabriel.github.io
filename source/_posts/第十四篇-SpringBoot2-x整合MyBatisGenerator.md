---
title: 第十四篇-SpringBoot2-x整合MyBatisGenerator
date: 2018/11/23
updated: 2018/11/23
---

在这里简单介绍一下如何整合`Mybatis`自动生成代码的插件`MybatisGenerator`

# 引入插件

需要在`pom.xml`文件中的`<build><plugins></plugins></build>`中加入以下设置

```
            <plugin>
				<groupId>org.mybatis.generator</groupId>
				<artifactId>mybatis-generator-maven-plugin</artifactId>
				<version>1.3.5</version>
				<!-- 添加一个mysql的依赖,防止等会找不到driverClass -->
				<dependencies>
				<dependency>
					<groupId>mysql</groupId>
					<artifactId>mysql-connector-java</artifactId>
					<version>5.1.28</version>
					<scope>runtime</scope>
				</dependency>
				</dependencies>
				<!-- mybatisGenerator 的配置 -->
				<configuration>
					<!-- generator 工具配置文件的位置 -->
					<configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
					<!-- 是否覆盖 -->
                    <!-- 此处要特别注意,如果不加这个设置会导致每次运行都会在原目录再次创建-->
					<overwrite>true</overwrite>
				</configuration>
			</plugin>
```

引入依赖后右侧的`maven project`侧边栏中的`Plugins`选项会多出一个`mybatis-generator`
![image.png](https://upload-images.jianshu.io/upload_images/9988457-d23cfb62068d6c10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 设置配置文件

接下来在`resources`文件夹中创建一个`generatorConfig.xml`文件用于自动构建代码的配置

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="MySQLTables" targetRuntime="MyBatis3" defaultModelType="flat">

        <!-- 公共设置 -->
        <commentGenerator>
            <!-- 是否取消自动生成时的注释 -->
            <property name="suppressAllComments" value="true"/>
            <!-- 是否取消在注释中加上时间 -->
            <property name="suppressDate" value="true"/>
        </commentGenerator>

        <!-- 链接数据库的配置 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver" connectionURL="jdbc:mysql:///test" userId="root" password="root"/>

        <!-- 关于生成实体类的设置 -->
        <!-- targetPackage 生成代码的目标目录 -->
        <!-- targetProject 目录所属位置 -->
        <javaModelGenerator targetPackage="priv.gabriel.model" targetProject="src/main/java">
            <!-- 在targetPackge的基础上根据schema再生成一层package 默认flase -->
            <property name="enableSubPackages" value="true"/>
            <!-- 是否在get方法中 对String类型的字段做空的判断 -->
            <property name="trimStrings" value="true"/>
            <!-- 是否生成一个包含所有字段的构造器 -->
            <property name="constructorBased" value="false"/>
            <!-- 是否创建一个不可变类-->
            <property name="immutable" value="false"/>
        </javaModelGenerator>

        <!--关于生成映射文件的设置-->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
            <!--同上-->
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>

        <!--关于生成dao层的设置-->
        <javaClientGenerator type="mapper" targetPackage="priv.gabriel.dao" targetProject="src/main/java">
            <!--同上-->
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <!--需要生成的代码对应的表名-->
        <table tableName="user"></table>
    </context>
</generatorConfiguration>
```

#运行插件
配置就结束了，最后运行一下，这里找到在`Maven Project`的`mybatis-generator:generator`选项，双击运行
![image.png](https://upload-images.jianshu.io/upload_images/9988457-cbee06bad2a30a43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#最后的成果
User.java

```
package priv.gabriel.model;

import java.util.Date;

public class User {
    private Integer id;

    private String username;

    private String pword;

    private Integer age;

    private Date utime;

    private Date ctime;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username == null ? null : username.trim();
    }

    public String getPword() {
        return pword;
    }

    public void setPword(String pword) {
        this.pword = pword == null ? null : pword.trim();
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Date getUtime() {
        return utime;
    }

    public void setUtime(Date utime) {
        this.utime = utime;
    }

    public Date getCtime() {
        return ctime;
    }

    public void setCtime(Date ctime) {
        this.ctime = ctime;
    }
}
```

UserMapper.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="priv.gabriel.dao.UserMapper">
  <resultMap id="BaseResultMap" type="priv.gabriel.model.User">
    <id column="id" jdbcType="INTEGER" property="id" />
    <result column="username" jdbcType="VARCHAR" property="username" />
    <result column="pword" jdbcType="VARCHAR" property="pword" />
    <result column="age" jdbcType="INTEGER" property="age" />
    <result column="utime" jdbcType="DATE" property="utime" />
    <result column="ctime" jdbcType="DATE" property="ctime" />
  </resultMap>
  <sql id="Example_Where_Clause">
    <where>
      <foreach collection="oredCriteria" item="criteria" separator="or">
        <if test="criteria.valid">
          <trim prefix="(" prefixOverrides="and" suffix=")">
            <foreach collection="criteria.criteria" item="criterion">
              <choose>
                <when test="criterion.noValue">
                  and ${criterion.condition}
                </when>
                <when test="criterion.singleValue">
                  and ${criterion.condition} #{criterion.value}
                </when>
                <when test="criterion.betweenValue">
                  and ${criterion.condition} #{criterion.value} and #{criterion.secondValue}
                </when>
                <when test="criterion.listValue">
                  and ${criterion.condition}
                  <foreach close=")" collection="criterion.value" item="listItem" open="(" separator=",">
                    #{listItem}
                  </foreach>
                </when>
              </choose>
            </foreach>
          </trim>
        </if>
      </foreach>
    </where>
  </sql>
  <sql id="Update_By_Example_Where_Clause">
    <where>
      <foreach collection="example.oredCriteria" item="criteria" separator="or">
        <if test="criteria.valid">
          <trim prefix="(" prefixOverrides="and" suffix=")">
            <foreach collection="criteria.criteria" item="criterion">
              <choose>
                <when test="criterion.noValue">
                  and ${criterion.condition}
                </when>
                <when test="criterion.singleValue">
                  and ${criterion.condition} #{criterion.value}
                </when>
                <when test="criterion.betweenValue">
                  and ${criterion.condition} #{criterion.value} and #{criterion.secondValue}
                </when>
                <when test="criterion.listValue">
                  and ${criterion.condition}
                  <foreach close=")" collection="criterion.value" item="listItem" open="(" separator=",">
                    #{listItem}
                  </foreach>
                </when>
              </choose>
            </foreach>
          </trim>
        </if>
      </foreach>
    </where>
  </sql>
  <sql id="Base_Column_List">
    id, username, pword, age, utime, ctime
  </sql>
  <select id="selectByExample" parameterType="priv.gabriel.model.UserExample" resultMap="BaseResultMap">
    select
    <if test="distinct">
      distinct
    </if>
    <include refid="Base_Column_List" />
    from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null">
      order by ${orderByClause}
    </if>
  </select>
  <select id="selectByPrimaryKey" parameterType="java.lang.Integer" resultMap="BaseResultMap">
    select
    <include refid="Base_Column_List" />
    from user
    where id = #{id,jdbcType=INTEGER}
  </select>
  <delete id="deleteByPrimaryKey" parameterType="java.lang.Integer">
    delete from user
    where id = #{id,jdbcType=INTEGER}
  </delete>
  <delete id="deleteByExample" parameterType="priv.gabriel.model.UserExample">
    delete from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
  </delete>
  <insert id="insert" parameterType="priv.gabriel.model.User">
    insert into user (id, username, pword,
      age, utime, ctime)
    values (#{id,jdbcType=INTEGER}, #{username,jdbcType=VARCHAR}, #{pword,jdbcType=VARCHAR},
      #{age,jdbcType=INTEGER}, #{utime,jdbcType=DATE}, #{ctime,jdbcType=DATE})
  </insert>
  <insert id="insertSelective" parameterType="priv.gabriel.model.User">
    insert into user
    <trim prefix="(" suffix=")" suffixOverrides=",">
      <if test="id != null">
        id,
      </if>
      <if test="username != null">
        username,
      </if>
      <if test="pword != null">
        pword,
      </if>
      <if test="age != null">
        age,
      </if>
      <if test="utime != null">
        utime,
      </if>
      <if test="ctime != null">
        ctime,
      </if>
    </trim>
    <trim prefix="values (" suffix=")" suffixOverrides=",">
      <if test="id != null">
        #{id,jdbcType=INTEGER},
      </if>
      <if test="username != null">
        #{username,jdbcType=VARCHAR},
      </if>
      <if test="pword != null">
        #{pword,jdbcType=VARCHAR},
      </if>
      <if test="age != null">
        #{age,jdbcType=INTEGER},
      </if>
      <if test="utime != null">
        #{utime,jdbcType=DATE},
      </if>
      <if test="ctime != null">
        #{ctime,jdbcType=DATE},
      </if>
    </trim>
  </insert>
  <select id="countByExample" parameterType="priv.gabriel.model.UserExample" resultType="java.lang.Long">
    select count(*) from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
  </select>
  <update id="updateByExampleSelective" parameterType="map">
    update user
    <set>
      <if test="record.id != null">
        id = #{record.id,jdbcType=INTEGER},
      </if>
      <if test="record.username != null">
        username = #{record.username,jdbcType=VARCHAR},
      </if>
      <if test="record.pword != null">
        pword = #{record.pword,jdbcType=VARCHAR},
      </if>
      <if test="record.age != null">
        age = #{record.age,jdbcType=INTEGER},
      </if>
      <if test="record.utime != null">
        utime = #{record.utime,jdbcType=DATE},
      </if>
      <if test="record.ctime != null">
        ctime = #{record.ctime,jdbcType=DATE},
      </if>
    </set>
    <if test="_parameter != null">
      <include refid="Update_By_Example_Where_Clause" />
    </if>
  </update>
  <update id="updateByExample" parameterType="map">
    update user
    set id = #{record.id,jdbcType=INTEGER},
      username = #{record.username,jdbcType=VARCHAR},
      pword = #{record.pword,jdbcType=VARCHAR},
      age = #{record.age,jdbcType=INTEGER},
      utime = #{record.utime,jdbcType=DATE},
      ctime = #{record.ctime,jdbcType=DATE}
    <if test="_parameter != null">
      <include refid="Update_By_Example_Where_Clause" />
    </if>
  </update>
  <update id="updateByPrimaryKeySelective" parameterType="priv.gabriel.model.User">
    update user
    <set>
      <if test="username != null">
        username = #{username,jdbcType=VARCHAR},
      </if>
      <if test="pword != null">
        pword = #{pword,jdbcType=VARCHAR},
      </if>
      <if test="age != null">
        age = #{age,jdbcType=INTEGER},
      </if>
      <if test="utime != null">
        utime = #{utime,jdbcType=DATE},
      </if>
      <if test="ctime != null">
        ctime = #{ctime,jdbcType=DATE},
      </if>
    </set>
    where id = #{id,jdbcType=INTEGER}
  </update>
  <update id="updateByPrimaryKey" parameterType="priv.gabriel.model.User">
    update user
    set username = #{username,jdbcType=VARCHAR},
      pword = #{pword,jdbcType=VARCHAR},
      age = #{age,jdbcType=INTEGER},
      utime = #{utime,jdbcType=DATE},
      ctime = #{ctime,jdbcType=DATE}
    where id = #{id,jdbcType=INTEGER}
  </update>
  <resultMap id="BaseResultMap" type="priv.gabriel.model.User">
    <id column="id" jdbcType="INTEGER" property="id" />
    <result column="username" jdbcType="VARCHAR" property="username" />
    <result column="pword" jdbcType="VARCHAR" property="pword" />
    <result column="age" jdbcType="INTEGER" property="age" />
    <result column="utime" jdbcType="DATE" property="utime" />
    <result column="ctime" jdbcType="DATE" property="ctime" />
  </resultMap>
  <sql id="Example_Where_Clause">
    <where>
      <foreach collection="oredCriteria" item="criteria" separator="or">
        <if test="criteria.valid">
          <trim prefix="(" prefixOverrides="and" suffix=")">
            <foreach collection="criteria.criteria" item="criterion">
              <choose>
                <when test="criterion.noValue">
                  and ${criterion.condition}
                </when>
                <when test="criterion.singleValue">
                  and ${criterion.condition} #{criterion.value}
                </when>
                <when test="criterion.betweenValue">
                  and ${criterion.condition} #{criterion.value} and #{criterion.secondValue}
                </when>
                <when test="criterion.listValue">
                  and ${criterion.condition}
                  <foreach close=")" collection="criterion.value" item="listItem" open="(" separator=",">
                    #{listItem}
                  </foreach>
                </when>
              </choose>
            </foreach>
          </trim>
        </if>
      </foreach>
    </where>
  </sql>
  <sql id="Update_By_Example_Where_Clause">
    <where>
      <foreach collection="example.oredCriteria" item="criteria" separator="or">
        <if test="criteria.valid">
          <trim prefix="(" prefixOverrides="and" suffix=")">
            <foreach collection="criteria.criteria" item="criterion">
              <choose>
                <when test="criterion.noValue">
                  and ${criterion.condition}
                </when>
                <when test="criterion.singleValue">
                  and ${criterion.condition} #{criterion.value}
                </when>
                <when test="criterion.betweenValue">
                  and ${criterion.condition} #{criterion.value} and #{criterion.secondValue}
                </when>
                <when test="criterion.listValue">
                  and ${criterion.condition}
                  <foreach close=")" collection="criterion.value" item="listItem" open="(" separator=",">
                    #{listItem}
                  </foreach>
                </when>
              </choose>
            </foreach>
          </trim>
        </if>
      </foreach>
    </where>
  </sql>
  <sql id="Base_Column_List">
    id, username, pword, age, utime, ctime
  </sql>
  <select id="selectByExample" parameterType="priv.gabriel.model.UserExample" resultMap="BaseResultMap">
    select
    <if test="distinct">
      distinct
    </if>
    <include refid="Base_Column_List" />
    from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null">
      order by ${orderByClause}
    </if>
  </select>
  <select id="selectByPrimaryKey" parameterType="java.lang.Integer" resultMap="BaseResultMap">
    select
    <include refid="Base_Column_List" />
    from user
    where id = #{id,jdbcType=INTEGER}
  </select>
  <delete id="deleteByPrimaryKey" parameterType="java.lang.Integer">
    delete from user
    where id = #{id,jdbcType=INTEGER}
  </delete>
  <delete id="deleteByExample" parameterType="priv.gabriel.model.UserExample">
    delete from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
  </delete>
  <insert id="insert" parameterType="priv.gabriel.model.User">
    insert into user (id, username, pword,
      age, utime, ctime)
    values (#{id,jdbcType=INTEGER}, #{username,jdbcType=VARCHAR}, #{pword,jdbcType=VARCHAR},
      #{age,jdbcType=INTEGER}, #{utime,jdbcType=DATE}, #{ctime,jdbcType=DATE})
  </insert>
  <insert id="insertSelective" parameterType="priv.gabriel.model.User">
    insert into user
    <trim prefix="(" suffix=")" suffixOverrides=",">
      <if test="id != null">
        id,
      </if>
      <if test="username != null">
        username,
      </if>
      <if test="pword != null">
        pword,
      </if>
      <if test="age != null">
        age,
      </if>
      <if test="utime != null">
        utime,
      </if>
      <if test="ctime != null">
        ctime,
      </if>
    </trim>
    <trim prefix="values (" suffix=")" suffixOverrides=",">
      <if test="id != null">
        #{id,jdbcType=INTEGER},
      </if>
      <if test="username != null">
        #{username,jdbcType=VARCHAR},
      </if>
      <if test="pword != null">
        #{pword,jdbcType=VARCHAR},
      </if>
      <if test="age != null">
        #{age,jdbcType=INTEGER},
      </if>
      <if test="utime != null">
        #{utime,jdbcType=DATE},
      </if>
      <if test="ctime != null">
        #{ctime,jdbcType=DATE},
      </if>
    </trim>
  </insert>
  <select id="countByExample" parameterType="priv.gabriel.model.UserExample" resultType="java.lang.Long">
    select count(*) from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
  </select>
  <update id="updateByExampleSelective" parameterType="map">
    update user
    <set>
      <if test="record.id != null">
        id = #{record.id,jdbcType=INTEGER},
      </if>
      <if test="record.username != null">
        username = #{record.username,jdbcType=VARCHAR},
      </if>
      <if test="record.pword != null">
        pword = #{record.pword,jdbcType=VARCHAR},
      </if>
      <if test="record.age != null">
        age = #{record.age,jdbcType=INTEGER},
      </if>
      <if test="record.utime != null">
        utime = #{record.utime,jdbcType=DATE},
      </if>
      <if test="record.ctime != null">
        ctime = #{record.ctime,jdbcType=DATE},
      </if>
    </set>
    <if test="_parameter != null">
      <include refid="Update_By_Example_Where_Clause" />
    </if>
  </update>
  <update id="updateByExample" parameterType="map">
    update user
    set id = #{record.id,jdbcType=INTEGER},
      username = #{record.username,jdbcType=VARCHAR},
      pword = #{record.pword,jdbcType=VARCHAR},
      age = #{record.age,jdbcType=INTEGER},
      utime = #{record.utime,jdbcType=DATE},
      ctime = #{record.ctime,jdbcType=DATE}
    <if test="_parameter != null">
      <include refid="Update_By_Example_Where_Clause" />
    </if>
  </update>
  <update id="updateByPrimaryKeySelective" parameterType="priv.gabriel.model.User">
    update user
    <set>
      <if test="username != null">
        username = #{username,jdbcType=VARCHAR},
      </if>
      <if test="pword != null">
        pword = #{pword,jdbcType=VARCHAR},
      </if>
      <if test="age != null">
        age = #{age,jdbcType=INTEGER},
      </if>
      <if test="utime != null">
        utime = #{utime,jdbcType=DATE},
      </if>
      <if test="ctime != null">
        ctime = #{ctime,jdbcType=DATE},
      </if>
    </set>
    where id = #{id,jdbcType=INTEGER}
  </update>
  <update id="updateByPrimaryKey" parameterType="priv.gabriel.model.User">
    update user
    set username = #{username,jdbcType=VARCHAR},
      pword = #{pword,jdbcType=VARCHAR},
      age = #{age,jdbcType=INTEGER},
      utime = #{utime,jdbcType=DATE},
      ctime = #{ctime,jdbcType=DATE}
    where id = #{id,jdbcType=INTEGER}
  </update>
  <resultMap id="BaseResultMap" type="priv.gabriel.model.User">
    <id column="id" jdbcType="INTEGER" property="id" />
    <result column="username" jdbcType="VARCHAR" property="username" />
    <result column="pword" jdbcType="VARCHAR" property="pword" />
    <result column="age" jdbcType="INTEGER" property="age" />
    <result column="utime" jdbcType="DATE" property="utime" />
    <result column="ctime" jdbcType="DATE" property="ctime" />
  </resultMap>
  <sql id="Example_Where_Clause">
    <where>
      <foreach collection="oredCriteria" item="criteria" separator="or">
        <if test="criteria.valid">
          <trim prefix="(" prefixOverrides="and" suffix=")">
            <foreach collection="criteria.criteria" item="criterion">
              <choose>
                <when test="criterion.noValue">
                  and ${criterion.condition}
                </when>
                <when test="criterion.singleValue">
                  and ${criterion.condition} #{criterion.value}
                </when>
                <when test="criterion.betweenValue">
                  and ${criterion.condition} #{criterion.value} and #{criterion.secondValue}
                </when>
                <when test="criterion.listValue">
                  and ${criterion.condition}
                  <foreach close=")" collection="criterion.value" item="listItem" open="(" separator=",">
                    #{listItem}
                  </foreach>
                </when>
              </choose>
            </foreach>
          </trim>
        </if>
      </foreach>
    </where>
  </sql>
  <sql id="Update_By_Example_Where_Clause">
    <where>
      <foreach collection="example.oredCriteria" item="criteria" separator="or">
        <if test="criteria.valid">
          <trim prefix="(" prefixOverrides="and" suffix=")">
            <foreach collection="criteria.criteria" item="criterion">
              <choose>
                <when test="criterion.noValue">
                  and ${criterion.condition}
                </when>
                <when test="criterion.singleValue">
                  and ${criterion.condition} #{criterion.value}
                </when>
                <when test="criterion.betweenValue">
                  and ${criterion.condition} #{criterion.value} and #{criterion.secondValue}
                </when>
                <when test="criterion.listValue">
                  and ${criterion.condition}
                  <foreach close=")" collection="criterion.value" item="listItem" open="(" separator=",">
                    #{listItem}
                  </foreach>
                </when>
              </choose>
            </foreach>
          </trim>
        </if>
      </foreach>
    </where>
  </sql>
  <sql id="Base_Column_List">
    id, username, pword, age, utime, ctime
  </sql>
  <select id="selectByExample" parameterType="priv.gabriel.model.UserExample" resultMap="BaseResultMap">
    select
    <if test="distinct">
      distinct
    </if>
    <include refid="Base_Column_List" />
    from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null">
      order by ${orderByClause}
    </if>
  </select>
  <select id="selectByPrimaryKey" parameterType="java.lang.Integer" resultMap="BaseResultMap">
    select
    <include refid="Base_Column_List" />
    from user
    where id = #{id,jdbcType=INTEGER}
  </select>
  <delete id="deleteByPrimaryKey" parameterType="java.lang.Integer">
    delete from user
    where id = #{id,jdbcType=INTEGER}
  </delete>
  <delete id="deleteByExample" parameterType="priv.gabriel.model.UserExample">
    delete from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
  </delete>
  <insert id="insert" parameterType="priv.gabriel.model.User">
    insert into user (id, username, pword,
      age, utime, ctime)
    values (#{id,jdbcType=INTEGER}, #{username,jdbcType=VARCHAR}, #{pword,jdbcType=VARCHAR},
      #{age,jdbcType=INTEGER}, #{utime,jdbcType=DATE}, #{ctime,jdbcType=DATE})
  </insert>
  <insert id="insertSelective" parameterType="priv.gabriel.model.User">
    insert into user
    <trim prefix="(" suffix=")" suffixOverrides=",">
      <if test="id != null">
        id,
      </if>
      <if test="username != null">
        username,
      </if>
      <if test="pword != null">
        pword,
      </if>
      <if test="age != null">
        age,
      </if>
      <if test="utime != null">
        utime,
      </if>
      <if test="ctime != null">
        ctime,
      </if>
    </trim>
    <trim prefix="values (" suffix=")" suffixOverrides=",">
      <if test="id != null">
        #{id,jdbcType=INTEGER},
      </if>
      <if test="username != null">
        #{username,jdbcType=VARCHAR},
      </if>
      <if test="pword != null">
        #{pword,jdbcType=VARCHAR},
      </if>
      <if test="age != null">
        #{age,jdbcType=INTEGER},
      </if>
      <if test="utime != null">
        #{utime,jdbcType=DATE},
      </if>
      <if test="ctime != null">
        #{ctime,jdbcType=DATE},
      </if>
    </trim>
  </insert>
  <select id="countByExample" parameterType="priv.gabriel.model.UserExample" resultType="java.lang.Long">
    select count(*) from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
  </select>
  <update id="updateByExampleSelective" parameterType="map">
    update user
    <set>
      <if test="record.id != null">
        id = #{record.id,jdbcType=INTEGER},
      </if>
      <if test="record.username != null">
        username = #{record.username,jdbcType=VARCHAR},
      </if>
      <if test="record.pword != null">
        pword = #{record.pword,jdbcType=VARCHAR},
      </if>
      <if test="record.age != null">
        age = #{record.age,jdbcType=INTEGER},
      </if>
      <if test="record.utime != null">
        utime = #{record.utime,jdbcType=DATE},
      </if>
      <if test="record.ctime != null">
        ctime = #{record.ctime,jdbcType=DATE},
      </if>
    </set>
    <if test="_parameter != null">
      <include refid="Update_By_Example_Where_Clause" />
    </if>
  </update>
  <update id="updateByExample" parameterType="map">
    update user
    set id = #{record.id,jdbcType=INTEGER},
      username = #{record.username,jdbcType=VARCHAR},
      pword = #{record.pword,jdbcType=VARCHAR},
      age = #{record.age,jdbcType=INTEGER},
      utime = #{record.utime,jdbcType=DATE},
      ctime = #{record.ctime,jdbcType=DATE}
    <if test="_parameter != null">
      <include refid="Update_By_Example_Where_Clause" />
    </if>
  </update>
  <update id="updateByPrimaryKeySelective" parameterType="priv.gabriel.model.User">
    update user
    <set>
      <if test="username != null">
        username = #{username,jdbcType=VARCHAR},
      </if>
      <if test="pword != null">
        pword = #{pword,jdbcType=VARCHAR},
      </if>
      <if test="age != null">
        age = #{age,jdbcType=INTEGER},
      </if>
      <if test="utime != null">
        utime = #{utime,jdbcType=DATE},
      </if>
      <if test="ctime != null">
        ctime = #{ctime,jdbcType=DATE},
      </if>
    </set>
    where id = #{id,jdbcType=INTEGER}
  </update>
  <update id="updateByPrimaryKey" parameterType="priv.gabriel.model.User">
    update user
    set username = #{username,jdbcType=VARCHAR},
      pword = #{pword,jdbcType=VARCHAR},
      age = #{age,jdbcType=INTEGER},
      utime = #{utime,jdbcType=DATE},
      ctime = #{ctime,jdbcType=DATE}
    where id = #{id,jdbcType=INTEGER}
  </update>
  <resultMap id="BaseResultMap" type="priv.gabriel.model.User">
    <id column="id" jdbcType="INTEGER" property="id" />
    <result column="username" jdbcType="VARCHAR" property="username" />
    <result column="pword" jdbcType="VARCHAR" property="pword" />
    <result column="age" jdbcType="INTEGER" property="age" />
    <result column="utime" jdbcType="DATE" property="utime" />
    <result column="ctime" jdbcType="DATE" property="ctime" />
  </resultMap>
  <sql id="Example_Where_Clause">
    <where>
      <foreach collection="oredCriteria" item="criteria" separator="or">
        <if test="criteria.valid">
          <trim prefix="(" prefixOverrides="and" suffix=")">
            <foreach collection="criteria.criteria" item="criterion">
              <choose>
                <when test="criterion.noValue">
                  and ${criterion.condition}
                </when>
                <when test="criterion.singleValue">
                  and ${criterion.condition} #{criterion.value}
                </when>
                <when test="criterion.betweenValue">
                  and ${criterion.condition} #{criterion.value} and #{criterion.secondValue}
                </when>
                <when test="criterion.listValue">
                  and ${criterion.condition}
                  <foreach close=")" collection="criterion.value" item="listItem" open="(" separator=",">
                    #{listItem}
                  </foreach>
                </when>
              </choose>
            </foreach>
          </trim>
        </if>
      </foreach>
    </where>
  </sql>
  <sql id="Update_By_Example_Where_Clause">
    <where>
      <foreach collection="example.oredCriteria" item="criteria" separator="or">
        <if test="criteria.valid">
          <trim prefix="(" prefixOverrides="and" suffix=")">
            <foreach collection="criteria.criteria" item="criterion">
              <choose>
                <when test="criterion.noValue">
                  and ${criterion.condition}
                </when>
                <when test="criterion.singleValue">
                  and ${criterion.condition} #{criterion.value}
                </when>
                <when test="criterion.betweenValue">
                  and ${criterion.condition} #{criterion.value} and #{criterion.secondValue}
                </when>
                <when test="criterion.listValue">
                  and ${criterion.condition}
                  <foreach close=")" collection="criterion.value" item="listItem" open="(" separator=",">
                    #{listItem}
                  </foreach>
                </when>
              </choose>
            </foreach>
          </trim>
        </if>
      </foreach>
    </where>
  </sql>
  <sql id="Base_Column_List">
    id, username, pword, age, utime, ctime
  </sql>
  <select id="selectByExample" parameterType="priv.gabriel.model.UserExample" resultMap="BaseResultMap">
    select
    <if test="distinct">
      distinct
    </if>
    <include refid="Base_Column_List" />
    from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
    <if test="orderByClause != null">
      order by ${orderByClause}
    </if>
  </select>
  <select id="selectByPrimaryKey" parameterType="java.lang.Integer" resultMap="BaseResultMap">
    select
    <include refid="Base_Column_List" />
    from user
    where id = #{id,jdbcType=INTEGER}
  </select>
  <delete id="deleteByPrimaryKey" parameterType="java.lang.Integer">
    delete from user
    where id = #{id,jdbcType=INTEGER}
  </delete>
  <delete id="deleteByExample" parameterType="priv.gabriel.model.UserExample">
    delete from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
  </delete>
  <insert id="insert" parameterType="priv.gabriel.model.User">
    insert into user (id, username, pword,
      age, utime, ctime)
    values (#{id,jdbcType=INTEGER}, #{username,jdbcType=VARCHAR}, #{pword,jdbcType=VARCHAR},
      #{age,jdbcType=INTEGER}, #{utime,jdbcType=DATE}, #{ctime,jdbcType=DATE})
  </insert>
  <insert id="insertSelective" parameterType="priv.gabriel.model.User">
    insert into user
    <trim prefix="(" suffix=")" suffixOverrides=",">
      <if test="id != null">
        id,
      </if>
      <if test="username != null">
        username,
      </if>
      <if test="pword != null">
        pword,
      </if>
      <if test="age != null">
        age,
      </if>
      <if test="utime != null">
        utime,
      </if>
      <if test="ctime != null">
        ctime,
      </if>
    </trim>
    <trim prefix="values (" suffix=")" suffixOverrides=",">
      <if test="id != null">
        #{id,jdbcType=INTEGER},
      </if>
      <if test="username != null">
        #{username,jdbcType=VARCHAR},
      </if>
      <if test="pword != null">
        #{pword,jdbcType=VARCHAR},
      </if>
      <if test="age != null">
        #{age,jdbcType=INTEGER},
      </if>
      <if test="utime != null">
        #{utime,jdbcType=DATE},
      </if>
      <if test="ctime != null">
        #{ctime,jdbcType=DATE},
      </if>
    </trim>
  </insert>
  <select id="countByExample" parameterType="priv.gabriel.model.UserExample" resultType="java.lang.Long">
    select count(*) from user
    <if test="_parameter != null">
      <include refid="Example_Where_Clause" />
    </if>
  </select>
  <update id="updateByExampleSelective" parameterType="map">
    update user
    <set>
      <if test="record.id != null">
        id = #{record.id,jdbcType=INTEGER},
      </if>
      <if test="record.username != null">
        username = #{record.username,jdbcType=VARCHAR},
      </if>
      <if test="record.pword != null">
        pword = #{record.pword,jdbcType=VARCHAR},
      </if>
      <if test="record.age != null">
        age = #{record.age,jdbcType=INTEGER},
      </if>
      <if test="record.utime != null">
        utime = #{record.utime,jdbcType=DATE},
      </if>
      <if test="record.ctime != null">
        ctime = #{record.ctime,jdbcType=DATE},
      </if>
    </set>
    <if test="_parameter != null">
      <include refid="Update_By_Example_Where_Clause" />
    </if>
  </update>
  <update id="updateByExample" parameterType="map">
    update user
    set id = #{record.id,jdbcType=INTEGER},
      username = #{record.username,jdbcType=VARCHAR},
      pword = #{record.pword,jdbcType=VARCHAR},
      age = #{record.age,jdbcType=INTEGER},
      utime = #{record.utime,jdbcType=DATE},
      ctime = #{record.ctime,jdbcType=DATE}
    <if test="_parameter != null">
      <include refid="Update_By_Example_Where_Clause" />
    </if>
  </update>
  <update id="updateByPrimaryKeySelective" parameterType="priv.gabriel.model.User">
    update user
    <set>
      <if test="username != null">
        username = #{username,jdbcType=VARCHAR},
      </if>
      <if test="pword != null">
        pword = #{pword,jdbcType=VARCHAR},
      </if>
      <if test="age != null">
        age = #{age,jdbcType=INTEGER},
      </if>
      <if test="utime != null">
        utime = #{utime,jdbcType=DATE},
      </if>
      <if test="ctime != null">
        ctime = #{ctime,jdbcType=DATE},
      </if>
    </set>
    where id = #{id,jdbcType=INTEGER}
  </update>
  <update id="updateByPrimaryKey" parameterType="priv.gabriel.model.User">
    update user
    set username = #{username,jdbcType=VARCHAR},
      pword = #{pword,jdbcType=VARCHAR},
      age = #{age,jdbcType=INTEGER},
      utime = #{utime,jdbcType=DATE},
      ctime = #{ctime,jdbcType=DATE}
    where id = #{id,jdbcType=INTEGER}
  </update>
</mapper>
```

UserMapper.java

```
package priv.gabriel.dao;

import java.util.List;
import org.apache.ibatis.annotations.Param;
import priv.gabriel.model.User;
import priv.gabriel.model.UserExample;

public interface UserMapper {
    long countByExample(UserExample example);

    int deleteByExample(UserExample example);

    int deleteByPrimaryKey(Integer id);

    int insert(User record);

    int insertSelective(User record);

    List<User> selectByExample(UserExample example);

    User selectByPrimaryKey(Integer id);

    int updateByExampleSelective(@Param("record") User record, @Param("example") UserExample example);

    int updateByExample(@Param("record") User record, @Param("example") UserExample example);

    int updateByPrimaryKeySelective(User record);

    int updateByPrimaryKey(User record);
}
```

可以看到已经帮我们生产了关于`CRUD`的绝大代码，再结合之前的通用`mapper`就可以解决绝大部分的问题啦
