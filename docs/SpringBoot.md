# 《快速启动脚手架SpringBoot》
## 01_springboot基本概念

#### 目标 了解Spring的优缺点

```
优点： IOC容器管理对象，方便解耦
	  AOP提供了通用功能的统一切面处理
	  方便集成各种优秀的框架
	
缺点： 配置文件越来越多越来越复杂	   
	  项目的依赖关系难以管理
```

#### 目标 SpringBoot的介绍

基本概念:

```
Springboot是一个脚手架框架，可以用最少的配置、最少的依赖很容易的创建独立的、生产级的基于Spring的项目,并可以直接去运行。
```

优点:

```
快速创建基于spring项目
内嵌web容器，不用部署直接启动
提供starter起步依赖，方便依赖管理
自动配置功能减少配置
提供各种项目监控、健康检查机制
完全不需要代码生成，不需要xml配置
```

核心功能:

```
起步依赖
概念： 起步依赖将实现某些功能的所有依赖坐标打包到一起
好处： 使用者只关注使用什么功能引什么starter,不用关注其内部都依赖哪些

自动装配 
概念： SpringBoot事先准备好了很多配置，会根据你的依赖情况决定该使用哪些配置
好处： 使用者只用配置和默认配置有变化的信息即可，甚至可以不用配置
```

## 02_快速入门

#### 目标 快速搭建一套web应用能够处理浏览器请求

```
1. 创建一个maven项目，pom文件中继承 spring-boot-starter-parent
2. 引入起步依赖 spring-boot-starter-web
3. 定义springboot的启动类，提供main方法

4. 写一个controller  /hello   ==>  欢迎使用springboot
```

继承父工程

```
	<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.18.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
```

引起步依赖

```
	<dependencies>
        <!-- 引入web起步依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

编写启动类

```
/**
 * springboot的启动类
 * @作者 itcast
 * @创建日期 2020/11/2 9:02
 **/
@SpringBootApplication(scanBasePackages = "com")
public class HelloApplication {
    public static void main(String[] args) {
        // 1. 参数:  引导类 标记了@SpringBootApplication 的类
        // 2. 参数:  外部传入的参数
        SpringApplication.run(HelloApplication.class,args);
    }
}
```

编写controller

```
@RestController
public class HelloController {
    @RequestMapping("hello")
    public String hello(){
        return "欢迎使用springboot";
    }
}
```

细节总结:

```
springboot提供的父工程spring-boot-starter-parent统一定义了依赖的信息
	我们在使用springboot时  可以直接引入起步依赖 不用关注版本号


起步依赖 spring-boot-starter-web 里面引入了对应web工程所需要的所有依赖
	如:  springmvc   tomcat   springweb


@SpringBootApplication
	@SpringBootConfiguration 继承 @Configuration 也是一个配置类
		我们可以通过@Bean来配置对象
	
	@ComponentScan  扫描注解
		默认扫描@SpringBootApplication注解所在的类，所属的包 下 及所有子包
		如果想修改 更改 scanBasePackages

```

#### 目标 能够通过SpringInitializr自动构建项目

```
springboot提供了 https://start.spring.io来快速的构建项目

1. 在idea中 点击 spring initializr菜单 进入自动构建页面
2. 填写项目的基本信息 
3. 勾选需要的起步依赖，选择对应的springboot版本
4. 点击finish  项目构建完毕
```

## 03_Springboot的配置

#### 目标 Springboot自动读取的配置及优先级

```
springboot启动后 会默认的去指定文件夹加载  名称为 application的文件，后缀为
.properties 或 .yml .yaml 

如果在同一目录下出现多个配置文件: springboot会都加载
		application.properties > .yml > .yaml
		属性名相同: 优先级高的生效
		属性名不同: 都生效
		
		项目目录:
			config:
				application.yml
		项目目录:
			application.yml
		项目目录:
			resources:
				config
					application.yml
		项目目录:
			resources:
				application.yml
		
```

#### 目标 演示配置的修改

查看默认属性:

https://docs.spring.io/spring-boot/docs/2.1.18.RELEASE/reference/html/common-application-properties.html

在配置文件中，也会有自动提示功能

更改web服务端口

```
server.port=9001 
```

更改项目访问目录

```
server.servlet.context-path=/demo
```

#### 目标 yml配置语法说明

配置普通数据：

```
语法： key: value		
如：   name: xiaoming
注意： : 和 value间一定要有空格
```

配置对象或map数据:

```
语法：  key:
		 key1: value1
		 key2: value2		
或： key: {key1: value1,key2: value2}
如：	person:
		name: xiaoming
			first: 1 (会报错)
		age: 3
注意：  相同缩进代表同一级别. name和age同一级别都是person的属性
		如果属性有了值就不能在有下级属性
```

配置集合数组:

```
语法：  key: 
		  - value1 					
		  - value2 					
或： key: [value1,value2]
注意：  -符号 一定要在同一级别,并且和属性间要有空格
```

配置对象集合

```
语法：  key: 
		- key1: value 
		  key2: value
		- key1: value 
		  key2: value
或： key: [{key1: value1,key2: value2},{key1: value1,key2: value2}]
注意：  -符号 一定要在同一级别,并且和属性间要有空格
```

#### 目标 配置内容的读取

1. 通过内置对象Environment读取

```java
@Autowired
Environment environment;  // spring中提供的环境对象
environment.getProperty("配置文件属性的key");
```

2. 通过@Value注解读取

```java
	@Value("${name}")
    String name;

    @Value("${person.name}")
    String personName;

    @Value("${city[0]}")
    String cityFirst;
```

3. 通过@ConfigurationProperties读取

```java
@Data
@Component
@ConfigurationProperties(prefix = "config-attributes")
public class ConfigModel {
    private String val;
    private int[] valArr;
    private List<String> cityList;
    private Map valMap;
    private List<Map> valMapList;
}
```

#### 目标 多环境配置与切换

定义三种配置, 开发 测试 生产环境的配置，并且可以自如切换

**配置方式一**

```yml
server:
  servlet:
    context-path: /demo
spring:
  profiles:
    active: test
---
spring:
  profiles: dev  # 开发环境的配置
server:
  port: 9001
---
spring:
  profiles: test  # 测试环境的配置
server:
  port: 9002
---
spring:
  profiles: prod # 生产环境的配置
server:
  port: 9003
```

**配置方式二**

```
如果所有环境配置都写到一个配置文件中，不好维护

可以定义多个环境配置文件的方式来实现: 

如: 
application-{环境名称}.yml

application-dev.yml
application-test.yml
application-prod.yml 

spring:
  profiles:
    active: prod  激活生产环境的配置
```

## 04_Springboot整合其它框架

#### 目标 整合SSM

POM引入依赖

```xml
	<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.18.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.itheima</groupId>
    <artifactId>springboot04-ssm</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot04-ssm</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.3</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
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

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

编写配置文件

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql:///javaee113?useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai
    username: root
    password: root
```

编写启动类

```java
@SpringBootApplication
@MapperScan("com.itheima.ssm.mapper") //mapper接口扫描
public class Springboot04SsmApplication {
    public static void main(String[] args) {
        SpringApplication.run(Springboot04SsmApplication.class, args);
    }
}
```

实体类

```java
@Data
public class User implements Serializable {
    private static final long serialVersionUID = -44397379241563010L;

    private Integer id;
    /**
     * 用户名称
     */
    private String username;
    /**
     * 生日
     */
    private Date birthday;
    /**
     * 性别
     */
    private String sex;
    /**
     * 地址
     */
    private String address;
}
```

mapper接口定义

```java
public interface UserMapper {
    List<User> findAll();
}

```

mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.itheima.ssm.mapper.UserMapper">
    <select id="findAll" resultType="com.itheima.ssm.pojo.User">
        select * from user
    </select>
</mapper>
```

service 实现类

```java
@Service
public class UserService {

    @Autowired
    UserMapper userMapper;
    public List<User> findAll(){
        return userMapper.findAll();
    }
}
```

controller类

```java
@RestController
@RequestMapping("user")
public class UserController {
    @Autowired
    UserService userService;
    @GetMapping
    public List<User> findAll(){
        return userService.findAll();
    }
}
```

浏览器测试:

http://localhost:8080/user

#### 目标 整合单元测试

```xml
引入 test依赖 即可以使用单元测试
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
```

```java
@RunWith(SpringRunner.class) // springboot 2.2版本后可以省略
// 如果单元测试的类 和 启动类的包名一致 那么可以不用指定classes属性
// 如果不在同一个包，需要指定启动类
@SpringBootTest(classes = Springboot04SsmApplication.class)
public class Springboot04SsmApplicationTests {

    @Autowired
    UserService userService;
    
    @Test
    public void findAll() {
        List<User> all = userService.findAll();
        all.forEach(System.out::println);
    }
}
```

#### 目标 整合事务处理

```
基于spring整合事务:
   编程式事务
   声明式事务
   	 xml  配置 tx:aop标签 
   	 开启事务注解,通过 @Transactional配置事务

		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
            <scope>test</scope>
        </dependency>
      
      该依赖已经自动配置了事务
      可以直接使用@Transactional配置事务
```

#### 目标 整合数据库连接池

```xml
		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
            <scope>test</scope>
        </dependency>
在jdbc依赖中，springboot给我们内置了连接池, HikariDataSource连接池如果要变成Druid连接池的话
  1. 需要引入依赖
          <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.1.22</version>
        </dependency>
  2. 更改datasource里面的配置
  		spring:
          datasource:
            driver-class-name: com.mysql.cj.jdbc.Driver
            url: jdbc:mysql:///javaee113?useUnicode=true&characterEncoding=utf8&serverTimezone=Asia/Shanghai
            username: root
            password: root
            type: com.alibaba.druid.pool.DruidDataSource
```

#### 目标 使用springboot整合redis

```
1.在springboot项目中，操作redis只需要引入
    spring-boot-starter-data-redis
2.配置redis的连接
	默认连接: localhost   port:6379
3.@Autowired注入 RedisTemplate对象 即可对redis进行操作
```

引入redis起步依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!-- 转json的依赖 -->
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>fastjson</artifactId>
	<version>1.2.74</version>
</dependency>
```

```java
@Service
@Transactional
public class UserService {
    @Autowired
    UserMapper userMapper;
    // 操作redis的模板类
    @Autowired
    StringRedisTemplate redisTemplate;
    public List<User> findAll(){
        // 1. 去redis中判断是否有集合数据
        String userListJson = redisTemplate.boundValueOps("user_list").get();
        // 判断缓存是否为空
        if(StringUtils.isEmpty(userListJson)){
            System.out.println("缓存中没有数据,从数据库中查询");
            // 没有  去数据库查询 并且存入到redis中
            List<User> all = userMapper.findAll();
            redisTemplate.boundValueOps("user_list").set(JSON.toJSONString(all));
            return all;
        }
        System.out.println("从缓存中查询到了数据");
        // 有    return
        return JSONArray.parseArray(userListJson,User.class);
    }
    public void insert(User user){
        userMapper.insert(user);
        // 清空缓存
        redisTemplate.delete("user_list");
    }
}
```

#### 目标 使用springboot整合日志打印

```
springboot web工程已经引入了日志依赖
在项目中可以直接日志

private static final Logger LOGGER = LoggerFactory.getLogger(UserService.class);


也可以使用lombok提供的注解 快捷性的使用日志
@Log4j2   @Slf4j
标记了注解后可以直接使用  log对象打印日志

logging:
  level:
#    root: info  # 全局日志级别
    com.itheima.ssm: info  # 指定包下的日志级别
  path: D:/myLog # 会在该文件夹下  生成日志文件
```

#### 目标 使用springboot整合thymeleaf视图

引入起步依赖

```xml
<!-- 整合 视图依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```

配置文件内容

```yml
spring:
  thymeleaf:
    prefix: classpath:/templates/
    suffix: .html
```

配置模板页面

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>欢迎光临</title>

</head>
<body background="/meinv002.jpg">
<h1>
    欢迎使用由 thymeleaf模板 <span th:text="${name}"></span>引擎提供的页面
</h1>
<table>
    <tr th:each="user : ${list}">
        <td th:text="${user.id}"> id </td>
        <td th:text="${user.username}"> username </td>
    </tr>
</table>
</body>
</html>
```

controller中指定返回页面

```java
/**
 * @作者 itcast
 * @创建日期 2020/11/2 11:29
 **/
@Controller
@RequestMapping("index")
public class IndexController {
    @Autowired
    UserService userService;
    @GetMapping
    public String findAll(Model model){
        String name = "xiaoming";
        List<User> all = userService.findAll();
        model.addAttribute("name",name);
        model.addAttribute("list",all);
        return "hello";
    }
} 
```

## 05_Springboot项目的部署和发布

#### 目标 基于jar包方式部署(推荐)

```
说明: springboot内置了tomcat,可以直接将springboot打包成jar包，运行即可
使用步骤：
	1） pom中添加插件 spring-boot-maven-plugin
	2） 通过 mvn clean package命令打包
	3） java -jar xxxxx.jar启动工程
	4） linux下后台运行: nohup java -jar xxxxxx.jar &
nohup java -jar xxx.jar --spring.profiles.active=test  &
```

#### 目标 基于war包方式部署(了解)

```
使用步骤:
	1) pom中更改打包方式 <packaging>war</packaging>
	2) 排除内置的tomcat依赖，重新引入tomcat依赖将scope改为provided
	3) 启动类继承SpringBootServletInitializer重写configure方法
	4) mvn clean package命令打包 
	5）将打包生成的war拷贝至tomcat工程webapps下 即可
	6）运行tomcat测试项目
```

## 06_Springboot自动装配原理分析

#### 目标 了解自动装配的原理

```

@SpringBootConfiguration  // 配置类

@EnableAutoConfiguration // 开启自动配置
@Import({AutoConfigurationImportSelector.class})
import 导入配置
 AutoConfigurationImportSelector 
 根据条件找到所有需要配置的类
 
 // 获取候选配置清单
 List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
           
           // 去掉重复的配置
           configurations = this.removeDuplicates(configurations);
           
           // 可以在@SpringBootApplication 指定要排除自动配置的类
            Set<String> exclusions = this.getExclusions(annotationMetadata, attributes);
            this.checkExcludedClasses(configurations, exclusions);
            configurations.removeAll(exclusions);
            configurations = this.filter(configurations, autoConfigurationMetadata);


@ComponentScan // 包扫描
```

#### 目标 自定义starter依赖

**创建Springboot工程** 

创建 myself-spring-boot-starter

**定义pom依赖**

```xml
	<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.17.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
    </dependencies>
```

主要是自动配置工程  spring-boot-maven-plugin 和 启动类就不用写了



**创建自动配置类**

```java
// 要自动配置的对象
public class Person {
    private Integer id;
    private String personName;
    @Override
    public String toString() {
        return "Person{" +
                "id=" + id +
                ", personName='" + personName + '\'' +
                '}';
    }
    public Integer getId() {
        return id;
    }
    public void setId(Integer id) {
        this.id = id;
    }
    public String getPersonName() {
        return personName;
    }
    public void setPersonName(String personName) {
        this.personName = personName;
    }
}
```

读取yml配置中属性,如果没配置设置好默认值

```java
/**
 * 属性封装类
 * @作者 itcast
 * @创建日期 2020/10/11 11:51
 **/
@Component
@ConfigurationProperties(prefix = "persion")
public class PersonProperties {
    // 读取 persion.id
    private Integer id = 11;// 默认值
    // 读取 persion.persionName
    private String personName = "小鬼"; // 默认值
    public Integer getId() {
        return id;
    }
    public void setId(Integer id) {
        this.id = id;
    }
    public String getPersonName() {
        return personName;
    }
    public void setPersonName(String personName) {
        this.personName = personName;
    }
}
```

配置类 配置@Bean对象，基于默认属性对象

```java
@Configuration // 当前是一个配置类 
@EnableConfigurationProperties(PersonProperties.class)
// PersonProperties 配置属性
public class AutoPersonConfig {
    @Bean
    Person person(PersonProperties personProperties){
        Person person = new Person();
        person.setId(personProperties.getId());
        person.setPersonName(personProperties.getPersonName());
        return person;
    }
}
```

**创建自动配置类的清单文件**

在resources下创建 **/META-INF/spring.factories** 配置

org.springframework.boot.autoconfigure.EnableAutoConfiguration固定写法的key

value: 就是我们**自动配置类的全限定类名**，如果有多个使用逗号隔开

```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.itcast.autoconfigure.UserConfig
```

配置后发布到本地仓库中  **maven** 的 **install**命令



