
## Maven高级

## 1.maven基础知识回顾

### 1.1 maven介绍

maven: 专家, 它是项目管理方面的专家

maven 是一个项目管理工具，主要作用是在项目开发阶段对Java项目进行依赖管理和项目构建，

![](.\img\图片1.png)

下面是它的作用：

1. **项目构建管理：** maven提供一套对项目生命周期管理的标准，开发人员、和测试人员统一使用maven进行项目构建。项目生命周期管理：编译、测试、打包、部署、运行。
2. **管理依赖（jar包）：** maven能够帮我们统一管理项目开发中需要的jar包； 
3. **管理插件：** maven能够帮我们统一管理项目开发过程中需要的插件；



​         maven

### 1.2 maven的仓库类型

maven的仓库可以分为3种：

| 仓库名称 | **作用**                                                     |
| -------- | ------------------------------------------------------------ |
| 本地仓库 | 相当于缓存，工程第一次会从远程仓库（互联网）去下载jar 包，将jar包存在本地仓库（在程序员的电脑上）。第二次不需要从远程仓库去下载。先从本地仓库找，如果找不到才会去远程仓库找。 |
| 中央仓库 | 是远程仓库，仓库中jar由专业团队（maven团队）统一维护。中央仓库的地址：<https://repo1.maven.org/maven2> |
| 私服     | 是远程仓库, 一般是在公司内部架设一台私人服务器对外公开。开发中经常使用的国内私服：阿里云 http://maven.aliyun.com/nexus/content/groups/public |

![1536321846073](img/1536321846073.png)



### 1.3 maven常用命令

```markdown
# mvn clean：
	调用clean生命周期的clean阶段，清理上一次构建项目生成的文件；
# mvn compile ：
	编译src/main/java中的java代码；
# mvn test ：
	编译并运行了test中内容 ；
# mvn package：
	将项目打包成可发布的文件，如jar或者war包； 
# mvn install ：
	发布项目到本地仓库 ；
```



### 1.4 maven坐标

​	Maven的一个核心的作用就是管理项目的依赖，引入我们所需的各种jar包等。为了能自动化的解析任何一个Java构件，Maven必须将这些Jar包或者其他资源进行唯一标识，这是管理项目的依赖的基础，也就是我们要说的坐标。包括我们自己开发的项目，也是要通过坐标进行唯一标识的，这样才能才其它项目中进行依赖引用。坐标的定义元素如下：

1. groupId：定义当前项目隶属的实际项目组。
2. artifactId：定义当前项目的名称；
3. version：定义当前项目的版本号；

通过上面三个参数我们就能够确定一个唯一版本号的jar包。

![1563638964921](img\1563638964921.png)



### 1.5 maven的依赖范围

依赖的jar默认情况可以在任何地方可用，可以通过`scope`标签设定其作用范围

这里的范围主要是指以下三种范围

（1）主程序范围有效（src/main目录范围内）

（2）测试程序范围内有效（src/test目录范围内）

（3）是否参与打包（package指令范围内）

![](img/50.png)



##### 指定依赖范围的方法

​	我们在导入依赖的时候，在<dependency>标签中使用<scope>设置即可，如下所示：

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-core</artifactId>
  <version>4.3.9.RELEASE</version>
  <!--compile是默认的依赖范围，可以不用写出来-->
  <scope>compile</scope>
</dependency>

<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  <scope>test</scope>
</dependency>

<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>servlet-api</artifactId>
  <version>2.5</version>
  <scope>provided</scope>
</dependency>

<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>5.0.8</version>
  <scope>runtime</scope>
</dependency>
```



## 2. maven的依赖传递

### 2.1 什么是依赖传递

在去介绍前，我们先来说下 Maven 中引入的包的类别：

**1.直接依赖包**

直接依赖包就是在 Maven 项目中的 pom 文件中直接导入的包，我们称之为直接依赖包。

依赖方：当前项目

被依赖：spring-context

![1600865224157](/img/1600865224157.png)



**2.依赖传递包**

在 Maven 项目中的 pom 文件中直接依赖包，也有依赖其他包的关系，我们称直接依赖包自身所需要的依赖包为依赖传递包。

依赖方：spring-context

被依赖：spring-aop 、spring-beans、spring-core、spring-expression

![1600865401414](/img/1600865401414.png)



**3.依赖传递**

直接依赖包自身将需要的依赖包导入到项目中，我们称之为包的依赖传递。

![1559549336921](.\img\图片3.png)



​                         

通过上面的图可以看到，我们的web项目直接依赖了spring-context，而spring-webcontextvc依赖了sping-aop、spring-beans等。最终的结果就是在我们的web项目中间接依赖了spring-aop、spring-beans等。

![1600912765732](/img/1600912765732.png)

### 2.2 什么是依赖冲突

由于依赖传递现象的存在。

​	spring-webmvc 依赖 spirng-beans-4.2.4，

​	spring-context 依赖 spring-beans-5.0.5，

​         这就造成了依赖冲突。

![1600866673155](/img/1600866673155.png)

maven工程中的两个直接依赖包中的依赖传递包，出现同种功能的并且包名一致，但是版本不一致，这种现象称之为依赖冲突。

依赖冲突：项目启动失败，运行失败。

### 2.3 解决依赖冲突方式

对于 Maven 依赖传递包的冲突，可以通过下面的方式来解决

```markdown
# 1.使用maven提供的依赖调节原则
	1.第一声明者优先
		主要针对依赖传递包
	2.路径近者优先
		直接依赖包优先级最高
		依赖传递包优先级低于直接依赖包
# 2.排除依赖
	在直接依赖包中可以将自身的依赖传递包排除
# 3.版本锁定
	使用版本锁定来固定依赖包的版本
```

下面一起来学习下，解决依赖冲突方式。



### 2.4 依赖调节原则

Maven 的依赖调节原则常用的有有以下：

```markdown
# 1.第一声明者优先
	主要针对依赖传递包
# 2.路径近者优先
	直接依赖包优先级最高
	依赖传递包优先级低于直接依赖包
```

#### 2.4.1 第一声明者优先

在 pom 文件中定义依赖，以先声明的依赖为准。其实就是根据坐标导入的顺序来确定最终使用哪个传递过来的依赖。

![1600866673155](/img/1600866673155.png)

结论：通过上图可以看到，spring-context 和spring-webmvc 都传递过来了spring-beans，但是因为spring-context 在前面，所以最终使用的 spring-beans 是由spring-context 传递过来的，而spring-webmvc 传递过来的spring-beans则被忽略了。

```markdown
# 第一声明者优先：
	在pom.xml文件中,先声明哪个jar包,就以那个jar包为主.
```

示例代码:

```xml
<dependencies>
    <!-- 直接依赖包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.5.RELEASE</version>
    </dependency>
    <!-- 直接依赖包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>4.2.4.RELEASE</version>
    </dependency>
</dependencies>
```

项目打包后的效果

![1600867938708](/img/1600867938708.png)



#### 2.4.2 路径近者优先

在 pom 文件中，spring-context 和 spring-webmvc 都将 spring-beans 作为依赖传递包，而项目中又将 spring-beans 作为直接依赖包导入到 pom 文件中，如下：

![1600868203680](/img/1600868203680.png)

结论：通过上图可以看到，spring-context 和spring-webmvc 都传递过来了spring-beans，但是因为项目将 spring-beans 作为直接依赖包导入，所以最终使用的 直接依赖包 spring -beans，其他的都会被忽略了。

```markdown
# 2.路径近者优先
	直接依赖包优先级最高
	依赖传递包优先级低于直接依赖包
```

示例代码:

```XML
<!-- 直接依赖包 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.0.5.RELEASE</version>
</dependency>
<!-- 直接依赖包 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.2.4.RELEASE</version>
</dependency>
<!-- 直接依赖包 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
    <version>5.0.6.RELEASE</version>
</dependency>
```

打包的结果：

![1600868508686](/img/1600868508686.png)



### 2.5 排除依赖

可以使用exclusions标签将传递过来的依赖排除出去。

![1600868869595](/img/1600868869595.png)

结论：通过上图可以看到，spring-context 已经将依赖传递spring-beans排除出去了，此时就会使用 spring-web 依赖传递包 spring-beans 。

示例代码:

```xml
 <!-- 直接依赖包 -->
 <dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-context</artifactId>
     <version>5.0.5.RELEASE</version>
     <exclusions>
         <exclusion>
             <groupId>org.springframework</groupId>
             <artifactId>spring-beans</artifactId>
         </exclusion>
     </exclusions>
 </dependency>
 <!-- 直接依赖包 -->
 <dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-webmvc</artifactId>
     <version>4.2.4.RELEASE</version>
 </dependency>
```

打包的结果：

![1600869045355](/img/1600869045355.png)



### 2.6 版本锁定

采用直接锁定版本的方法确定依赖jar包的版本，版本锁定后则不考虑依赖的声明顺序或依赖的路径，以锁定的版本为准添加到工程中，此方法在企业开发中经常使用。

版本锁定的使用方式：

第一步：在dependencyManagement标签中锁定依赖的版本

第二步：在dependencies标签中声明需要导入的maven坐标

**1.在dependencyManagement标签中锁定依赖的版本**

![1600869883265](/img/1600869883265.png)



PS：dependencyManager 标签中的内容，并不会导入jar包到项目中，它只负责对包版本进行锁定。



**2.在dependencies标签中声明需要导入的maven坐标**

![1600915523227](/img/1600915523227.png)

由于 dependencyManager  中已经对 spring-beans 进行了版本的锁定，所以直接依赖包 spring-webmvc 和 spring-context 会将 spring-beans 依赖版本提升为 5.0.6 版本。

示例代码:

```xml
<!-- 依赖 jar 的版本锁定 -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>5.0.6.RELEASE</version>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <!-- 直接依赖包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.5.RELEASE</version>
    </dependency>
    <!-- 直接依赖包 -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>4.2.4.RELEASE</version>
    </dependency>
</dependencies>
```

打包效果：

![1600870619145](/img/1600870619145.png)





## 3.基于maven构建SSM工程案例（复习）

今天内容需要参考 SpringMVC -day03的内容资料

#### 3.1 创建项目导入jar包坐标

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itheima</groupId>
    <artifactId>ssm02</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <spring.version>5.0.5.RELEASE</spring.version>
        <springmvc.version>5.0.5.RELEASE</springmvc.version>
        <mybatis.version>3.4.5</mybatis.version>
    </properties>
    
    <!--锁定jar版本-->
    <dependencyManagement>
        <dependencies>
            <!-- Mybatis -->
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>${mybatis.version}</version>
            </dependency>
            <!-- springMVC -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-webmvc</artifactId>
                <version>${springmvc.version}</version>
            </dependency>
            <!-- spring -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-aop</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-web</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-expression</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context-support</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-test</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-jdbc</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-tx</artifactId>
                <version>${spring.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>


    <dependencies>
        <!-- Mybatis和mybatis与spring的整合 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.6.4</version>
        </dependency>

        <!-- MySql驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.26</version>
        </dependency>


        <!-- springMVC核心-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
        </dependency>
        <!-- spring相关 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-expression</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
        </dependency>
        <!-- aspectJ坐标 切入点表达式-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.7</version>
        </dependency>


        <!-- jackson -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.9.4</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.4</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.9.4</version>
        </dependency>

        <!-- Servlet的jar包 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <!-- jsp的jar包 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.0</version>
            <scope>provided</scope>
        </dependency>
        <!-- jstl的jar包-->
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

        <!-- junit测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>
    
    
    
<!--设置插件-->
    <build>
        <plugins>
            <!--JDK编译插件-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.2</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>


            <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <version>2.2</version>
                <configuration>
                    <path>/ssm02</path>
                    <port>80</port>
                    <uriEncoding>utf-8</uriEncoding>
                </configuration>
            </plugin>

        </plugins>
    </build>

</project>
```

web.xml

```XML
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">


    <context-param>
        <param-name>contextConfigLocation</param-name>
<!--        <param-value>classpath:applicationContext-dao.xml,classpath:applicationContext-tx.xml</param-value>-->
        <!--
            classpath:applicationContext-*.xml
                加载当前项目下的（classpath）以applicationContext-的spring配置文件
         -->
        <param-value>classpath:applicationContext-*.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>


    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>5</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>


</web-app>
```

applicationContext-dao.xml

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!-- spring整合Mybatis的sqlsessionfactorybean -->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean" id="sessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="typeAliasesPackage" value="com.itheima.domain"></property>
        <property name="configLocation" value="classpath:sqlMapConfig.xml"></property>
    </bean>

    <!-- spring整合mapper扫描（Mybatis的代理对象） -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer" id="scannerConfigurer">
        <property name="basePackage" value="com.itheima.dao"></property>
    </bean>


</beans>
```

applicationContext-tx.xml

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!-- 扫描包 -->
    <!--
        component-scan base-package可以扫描多个包
                包1,包2...
     -->
    <context:component-scan base-package="com.itheima.service" />

    <!-- 加载properties文件 -->
    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>

    <!-- 配置数据库连接池 -->
    <bean class="org.springframework.jdbc.datasource.DriverManagerDataSource" id="dataSource">
        <property name="username"                          value="${jdbc.username}"></property>
        <property name="password"                          value="${jdbc.password}"></property>
        <property name="url"                               value="${jdbc.url}"></property>
        <property name="driverClassName"                   value="${jdbc.driver}"></property>
    </bean>

    <!-- 配置事务管理器 -->
    <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="transactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean>

    <!-- 声明式事务 -->
    <tx:annotation-driven transaction-manager="transactionManager" />

</beans>
```

spring-mvc.xml

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.itheima.web">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <mvc:annotation-driven />

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="viewResolver">
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>

    <!-- DefaultServletHttpRequestHandler -->
    <mvc:default-servlet-handler />


</beans>
```

sqlMapConfig.xml

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
        <!--开启全局加载-->
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="logImpl" value="LOG4J" />
    </settings>

</configuration>
```

jdbc.properties

```XML
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/case3?characterEncoding=utf8
jdbc.username=root
jdbc.password=root
```

log4j.properties

```XML
# Set root category priority to INFO and its only appender to CONSOLE.
#log4j.rootCategory=INFO, CONSOLE            debug   info   warn error fatal
log4j.rootCategory=info, CONSOLE

# Set the enterprise logger category to FATAL and its only appender to CONSOLE.
log4j.logger.org.apache.axis.enterprise=FATAL, CONSOLE

# CONSOLE is set to be a ConsoleAppender using a PatternLayout.
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n
```

Account

```JAVA
package com.itheima.domain;

import java.io.Serializable;

/**
 * <p></p>
 *
 * @Description:
 */
public class Account implements Serializable {

    private String id;
    private String name;
    private String money;

    public Account() {
    }

    public Account(String id, String name, String money) {
        this.id = id;
        this.name = name;
        this.money = money;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getMoney() {
        return money;
    }

    public void setMoney(String money) {
        this.money = money;
    }


    @Override
    public String toString() {
        return "Account{" +
                "id='" + id + '\'' +
                ", name='" + name + '\'' +
                ", money='" + money + '\'' +
                '}';
    }
}
```



AccountMapper

```java
package com.itheima.dao;

import com.itheima.domain.Account;
import org.apache.ibatis.annotations.Select;

import java.util.List;

/**
 * <p></p>
 *
 * @Description:
 */
public interface AccountMapper {

    @Select("select * from account")
    List<Account> selectAll();
}
```

IAccountService

```JAVA
package com.itheima.service;

import com.itheima.domain.Account;

import java.util.List;

/**
 * <p></p>
 *
 * @Description:
 */
public interface IAccountService {

    List<Account> findAll();

}

```

AccountServiceImpl

```JAVA
package com.itheima.service.impl;

import com.itheima.dao.AccountMapper;
import com.itheima.domain.Account;
import com.itheima.service.IAccountService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * <p></p>
 *
 * @Description:
 */
@Service
public class AccountServiceImpl implements IAccountService {

    @Autowired
    private AccountMapper accountMapper;



    public List<Account> findAll() {

        // 创建集合数据--伪数据
        // Account account1=new Account("1","蕾蕾","4654564646546");
        // Account account2=new Account("2","峥峥","4654564646546");
        // Account account3=new Account("3","浩浩","0.1");
        //
        // List<Account> list = new ArrayList<>();
        //
        // Collections.addAll(list, account1, account2, account3);

        List<Account> accounts = accountMapper.selectAll();
        return accounts;
    }
}
```

AccountController

```JAVA
package com.itheima.web;

import com.itheima.domain.Account;
import com.itheima.service.IAccountService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.List;

/**
 * <p></p>
 */
@Controller
@RequestMapping("account")
public class AccountController {

    @Autowired
    private IAccountService accountService;

    @RequestMapping("list")
    @ResponseBody
    public List findAll() {
        List<Account> all = accountService.findAll();
        return all;
    }

}
```





## 4.分模块构建maven工程

### 4.1 分模块构建maven工程分析

在现实生活中，汽车厂家进行汽车生产时，由于整个生产过程非常复杂和繁琐，工作量非常大，所以车场都会将整个汽车的部件分开生产，最终再将生产好的部件进行组装，形成一台完整的汽车。

![1559550879535](.\img\图片12.png)

![1559550904100](.\img\图片13.png)

```java
开发模式:
	按照功能模块开发:
		用户模块: 跟用户表相关的功能都属于用户模块
            注册,激活,登录,退出...
            前端 <--> web <--> service <--> dao <--> 数据库
        线路模块
        分类模块
        收藏模块
	按照分层模块开发:  ★★★
		前端:
		web模块:
		service模块: 
		dao模块:
		pojo:
		util:
```

### 4.2 maven工程的继承

在Java语言中，类之间是可以继承的，通过继承，子类就可以引用父类中非private的属性和方法。同样，在maven工程之间也可以继承，子工程继承父工程后，就可以使用在父工程中引入的依赖。继承的目的是为了消除重复代码。

![1600881898559](/img/1600881898559.png)



通过上面的结构图可以看到：

```markdown
# 1.聚合项目
	类型：pom
	作用：
		1.★★★聚合子项目，可以通过操作父项目来统一维护子项目★★★---（聚合的体现）
		2.★★★可以对子项目的依赖包的版本进行统一管理★★★  ---(继承的体现)
		3.★★★可以将子项目公共的资源提取到父项目中，子项目会自动继承父项目中的资源★★★---(继承的体现)
# 2.子项目-jar
	类型：jar
	作用：
		1.单独维护一个模块内容（例如：对实体类的管理，对业务类的管理...）
		2.每个子项目可以将其进行作为依赖导入到自己的工程中
# 3.子项目-war
	类型：war
	作用：
		1.javaWeb项目的表现层，主要来将项目在web项目中进行运行
		2.对其他的模块进行依赖，将其他模块导入到web项目中
```

```markdown
# 1.父项目表达子项目
  <modules>
     <module>maven_pojo</module>
     <module>maven_dao</module>
     <module>maven_service</module>
     <module>maven_web</module>
  </modules>
# 2.子项目表达父项目的继承关系
  <parent>
    <artifactId>maven_parent</artifactId>
    <groupId>com.itheima</groupId>
    <version>1.0-SNAPSHOT</version>
  </parent>
# 3.子项目的依赖表达
  maven_dao依赖maven_pojo
 	<!-- 子项目中的依赖关系
    	提示：如果在依赖后，出现报错（红色字体）
    	将parent进行install
 	-->
	<dependency>
    	<groupId>com.itheima</groupId>
    	<artifactId>maven_pojo</artifactId>
    	<version>1.0-SNAPSHOT</version>
	</dependency>
```



### 4.3 maven工程的聚合

在maven工程的pom.xml文件中可以使用<modules>标签将其他maven工程聚合到一起，聚合的目的是为了==进行统一操作==。

例如拆分后的maven工程有多个，如果要进行打包，就需要针对每个工程分别执行打包命令，操作起来非常繁琐。这时就可以使用<modules>标签将这些工程统一聚合到maven工程中，需要打包的时候，只需要在此工程中执行一次打包命令，其下被聚合的工程就都会被打包了。

![1559551000245](.\img\图片15.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itheima</groupId>
    <artifactId>maven_parent</artifactId>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>maven_pojo</module>
        <module>maven_dao</module>
        <module>maven_service</module>
        <module>maven_web</module>
    </modules>

    <!-- pom：代表当前项目为父项目（聚合项目） -->
    <packaging>pom</packaging>

    <dependencyManagement>

    </dependencyManagement>

    <!-- 添加jar文件：父项目中添加 -->
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

maven_parent进行打包，也会将其子项目一起打包。

![1600931151164](/img/1600931151164.png)







### 4.4 分模块构建ssm工程具体实现

1）ssm_parent为父工程，其余工程为子工程，都继承父工程maven_parent

2）ssm_parent工程将其子工程都进行了聚合 

3）子工程之间存在依赖关系，比如ssm_dao依赖， ssm_pojo、ssm_service依赖ssm_dao、 ssm_web依赖ssm_service



##### ①父工程ssm_parent构建

创建父工程,锁定jar包版本,其他子工程继承父工程直接导入jar包即可,不需要再设置jar包的版本了

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itheima</groupId>
    <artifactId>ssm_parent</artifactId>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>ssm_pojo</module>
        <module>ssm_dao</module>
        <module>ssm_service</module>
        <module>ssm_web</module>
    </modules>
    <packaging>pom</packaging>

    <!--
        1.★★★聚合子项目，可以通过操作父项目来统一维护子项目★★★（聚合的体现）
        2.★★★可以对子项目的依赖包的版本进行统一管理★★★  (继承的体现)
        3.★★★可以将子项目公共的资源提取到父项目中，子项目会自动继承父项目中的资源★★★(继承的体现)
     -->
    <!-- 对子项目的依赖jar版本进行控制 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <spring.version>5.0.5.RELEASE</spring.version>
        <springmvc.version>5.0.5.RELEASE</springmvc.version>
        <mybatis.version>3.4.5</mybatis.version>
        <ssm.version>1.0-SNAPSHOT</ssm.version>
    </properties>

    <!--锁定jar版本-->
    <dependencyManagement>
        <dependencies>
            <!-- 对子项目的版本控制 -->
            <dependency>
                <groupId>com.itheima</groupId>
                <artifactId>ssm_pojo</artifactId>
                <version>${ssm.version}</version>
            </dependency>

            <dependency>
                <groupId>com.itheima</groupId>
                <artifactId>ssm_dao</artifactId>
                <version>${ssm.version}</version>
            </dependency>

            <dependency>
                <groupId>com.itheima</groupId>
                <artifactId>ssm_service</artifactId>
                <version>${ssm.version}</version>
            </dependency>

            <dependency>
                <groupId>com.itheima</groupId>
                <artifactId>ssm_web</artifactId>
                <version>${ssm.version}</version>
            </dependency>


            <!-- Mybatis -->
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>${mybatis.version}</version>
            </dependency>
            <!-- springMVC -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-webmvc</artifactId>
                <version>${springmvc.version}</version>
            </dependency>
            <!-- spring -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-aop</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-web</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-expression</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context-support</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-test</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-jdbc</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-tx</artifactId>
                <version>${spring.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>


    <!-- 子项目就会自动的继承编译器版本 -->
    <!--设置插件-->
    <build>
        <plugins>
            <!--JDK编译插件-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.2</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

##### ②子工程ssm_pojo构建

 此工程的作用是,存放pojo对象

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>ssm_parent</artifactId>
        <groupId>com.itheima</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>ssm_pojo</artifactId>

    <!-- 如果javabean使用Lombok，需要导入Lombok -->
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.12</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>


</project>
```
在pojo模块中创建对象

![1600934231542](/img/1600934231542.png)

Account实体

```java
public class Account {
    private Integer id;
    private String name;
    private Float money;
    // get set方法...
}
```



##### ③子工程ssm_dao构建

   配置ssm_dao工程的pom.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>ssm_parent</artifactId>
        <groupId>com.itheima</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>ssm_dao</artifactId>

    <!-- 将Mybatis所相关的内容导入 -->
    <dependencies>
        <!-- 将子项目 pojo导入 -->
        <dependency>
            <groupId>com.itheima</groupId>
            <artifactId>ssm_pojo</artifactId>
        </dependency>

        <!-- Mybatis和mybatis与spring的整合 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.6.4</version>
        </dependency>
        <!-- MySql驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.26</version>
            <scope>runtime</scope>
        </dependency>
    </dependencies>


</project>
```

​ 

创建DAO接口和Mapper映射文件

![1600934268651](/img/1600934268651.png)

```java
package com.itheima.dao;

import com.itheima.pojo.Account;
import org.apache.ibatis.annotations.Select;
import java.util.List;
public interface AccountMapper {
    @Select("select * from account ")
    List<Account> findAll();
}
```

​ 在resources目录下创建spring配置文件applicationContext-dao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!-- spring整合Mybatis的sqlsessionfactorybean -->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean" id="sessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="typeAliasesPackage" value="com.itheima.domain"></property>
        <property name="configLocation" value="classpath:sqlMapConfig.xml"></property>
    </bean>

    <!-- spring整合mapper扫描（Mybatis的代理对象） -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer" id="scannerConfigurer">
        <property name="basePackage" value="com.itheima.dao"></property>
    </bean>


</beans>
```

sqlMapConfig.xml配置文件

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
        <!--开启全局加载-->
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="logImpl" value="LOG4J" />
    </settings>

</configuration>
```

log4j.properties


```properties
# Set root category priority to INFO and its only appender to CONSOLE.
#log4j.rootCategory=INFO, CONSOLE            debug   info   warn error fatal
log4j.rootCategory=INFO, CONSOLE

# Set the enterprise logger category to FATAL and its only appender to CONSOLE.
log4j.logger.org.apache.axis.enterprise=FATAL, CONSOLE

# CONSOLE is set to be a ConsoleAppender using a PatternLayout.
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern=%d{ISO8601} %-6r [%15.15t] %-5p %30.30c %x - %m\n
```

jdbc.properties

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/case3?characterEncoding=utf8
jdbc.username=root
jdbc.password=root
```





##### ④子工程ssm_service构建

配置ssm_service工程的pom.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>ssm_parent</artifactId>
        <groupId>com.itheima</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>ssm_service</artifactId>

    <!-- spring核心包和spring事务 -->
    <dependencies>
        <!-- 引入dao依赖 -->
        <!-- 将子项目 pojo导入 -->
        <dependency>
            <groupId>com.itheima</groupId>
            <artifactId>ssm_dao</artifactId>
        </dependency>

        <!-- spring相关 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-expression</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
        </dependency>
        <!-- aspectJ坐标 切入点表达式-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.7</version>
        </dependency>
    </dependencies>


</project>
```
![1600934452955](/img/1600934452955.png)



创建Service接口和实现类

```java
package com.itheima.service;

import com.itheima.pojo.Account;
import java.util.List;

public interface IAccountService {
    List<Account> findAll();
}
```

```java
package com.itheima.service.impl;

import com.itheima.dao.AccountMapper;
import com.itheima.domain.Account;
import com.itheima.service.IAccountService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * <p></p>
 *
 * @Description:
 */
@Service
public class AccountServiceImpl implements IAccountService {

    @Autowired
    private AccountMapper accountMapper;


    public List<Account> findAll() {
        List<Account> accountList = accountMapper.findAll();

        return accountList;
    }
}

```

创建spring配置文件applicationContext-tx.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">


    <!-- 开启包扫描
     Spring只管扫描Service层
         1.管理所有的Service实现类
         2.声明式事务
    -->
    <context:component-scan base-package="com.itheima.service" />

    <!-- 加载配置文件 jdbc.properties -->
    <context:property-placeholder location="classpath:jdbc.properties" />


    <!-- 数据库连接池 -->
    <bean class="org.springframework.jdbc.datasource.DriverManagerDataSource" id="dataSource">
        <property name="url"                value="${jdbc.url}"></property>
        <property name="username"           value="${jdbc.username}"></property>
        <property name="password"           value="${jdbc.password}"></property>
        <property name="driverClassName"    value="${jdbc.driver}"></property>
    </bean>

    <!-- 声明式事务 -->
    <!-- 事务管理器 -->
    <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="transactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>


    <!-- 注解形式的声明式事务 -->
    <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>

</beans>
```



##### ⑤子工程ssm_web构建

​    第一步：创建ssm_web工程，注意打包方式为war

​    第二步：配置ssm_web工程的pom.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>ssm_parent</artifactId>
        <groupId>com.itheima</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <packaging>war</packaging>

    <artifactId>ssm_web</artifactId>
    <dependencies>
        <!-- 导入service包 -->
        <dependency>
            <groupId>com.itheima</groupId>
            <artifactId>ssm_service</artifactId>
        </dependency>

        <!-- springMVC核心-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
        </dependency>


        <!-- jackson -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.9.4</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.9.4</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.9.4</version>
        </dependency>

        <!-- Servlet的jar包 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <!-- jsp的jar包 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.0</version>
            <scope>provided</scope>
        </dependency>
        <!-- jstl的jar包-->
        <dependency>
            <groupId>jstl</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

        <!-- junit测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>


    </dependencies>


    <!--设置插件-->
    <!-- maven的tomcat插件运行时的特点：
            将war项目的所有依赖会从本地仓库中来寻找
            每次更改代码，都要去安装项目
     -->
    <build>
        <plugins>

            <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <version>2.2</version>
                <configuration>
                    <path>/ssm02</path>
                    <port>80</port>
                    <uriEncoding>utf-8</uriEncoding>
                </configuration>
            </plugin>

        </plugins>
    </build>

</project>
```


![1600934556304](/img/1600934556304.png)   





 第三步：创建Controller

```java
package com.itheima.web;

import com.itheima.domain.Account;
import com.itheima.service.IAccountService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * <p></p>
 */
@RestController
@RequestMapping("account")
public class AccountController {

    @Autowired
    private IAccountService accountService;

    @GetMapping("list")
    public List<Account> findAll() {
        List<Account> all = accountService.findAll();

        return all;
    }
}
```

​    第四步：配置web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">


    <context-param>
        <param-name>contextConfigLocation</param-name>
        <!--        <param-value>classpath:applicationContext-dao.xml,classpath:applicationContext-tx.xml</param-value>-->
        <!--
            classpath:applicationContext-*.xml
                加载当前项目下的（classpath）以applicationContext-的spring配置文件
         -->
<!--        <param-value>classpath:applicationContext-*.xml</param-value>-->

        <!-- classpath* 可以加载依赖包中的配置文件 -->
        <param-value>classpath*:applicationContext-*.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>


    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>5</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>


</web-app>
```

​    第六步：创建springmvc配置文件springmvc.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.itheima.web">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <mvc:annotation-driven />

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="viewResolver">
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>

    <!-- DefaultServletHttpRequestHandler -->
    <mvc:default-servlet-handler />

</beans>
```







## 5. maven私服（了解）

### 5.1 私服说明 

maven仓库分为本地仓库和远程仓库，而远程仓库又分为maven中央仓库、其他远程仓库和私服（私有服务器）。其中，中央仓库是由maven官方提供的，而私服就需要我们自己搭建了。

maven私服就是公司局域网内的maven远程仓库，每个员工的电脑上安装maven软件并且连接maven私服，程序员可以将自己开发的项目打成jar并发布到私服，其它项目组成员就可以从私服下载所依赖的jar。私服还充当一个代理服务器的角色，当私服上没有jar包时会从maven中央仓库自动下载。

nexus 是一个maven仓库管理器（其实就是一个软件），nexus可以充当maven私服，同时nexus还提供强大的仓库管理、构件搜索等功能。

![1600935645214](/img/1600935645214.png)

### 5.2 搭建maven私服

①下载nexus

https://help.sonatype.com/repomanager2/download/download-archives---repository-manager-oss

②安装nexus

将下载的压缩包进行解压，进入bin目录

![1559551510928](.\img\图片17.png)

打开cmd窗口并进入上面bin目录下，执行`nexus.bat install`命令安装服务（==注意需要以管理员身份运行cmd命令==）

![1559551531544](.\img\图片18.png)

③启动nexus

经过前面命令已经完成nexus的安装，可以通过如下两种方式启动nexus服务：

方式一：在Windows系统服务中启动nexus

![1559551564441](.\img\图片19.png)

方式二：在命令行执行`nexus.bat start`命令启动nexus

![1559551591730](.\img\图片20.png)

④访问nexus

启动nexus服务后，访问http://localhost:8081/nexus

点击右上角LogIn按钮，进行登录。使用默认用户名`admin`和密码`admin123`登录系统

登录成功后点击左侧菜单Repositories可以看到nexus内置的仓库列表（如下图）

![1559551620133](.\img\图片.png)

![image-20200609162746460](img/image-20200609162746460.png)

nexus仓库类型

通过前面的仓库列表可以看到，nexus默认内置了很多仓库，这些仓库可以划分为4种类型，每种类型的仓库用于存放特定的jar包，具体说明如下：

①hosted，宿主仓库，部署自己的jar到这个类型的仓库，包括Releases和Snapshots两部分，Releases为公司内部发布版本仓库、 Snapshots为公司内部测试版本仓库 

```java
releases: ★发布版,经过测试的代码
snapshots: ★快照版,内部使用的测试版仓库
	我们的项目打好的包,究竟存放到哪个仓库,取决于<version>1.0-SNAPSHOT</version> 
3rd party: 存放第三方的jar包
    Oracle的jar,这个jar包是由Oracle公司提供的,在maven的中央仓库中是不存在的.
    要想使用这类jar包,需要从对应的官网上下载,下载下来以后可以安装到私服或本地仓库.
```

②proxy，代理仓库，用于代理远程的公共仓库，如maven中央仓库，用户连接私服，私服自动去中央仓库下载jar包或者插件

```java
Apache Snapshots: Apache仓库下载的jar包
Central: ★中央仓库下载的jar包
```

③group，仓库组，用来合并多个hosted/proxy仓库，通常我们配置自己的maven连接仓库组

```java
仓库组:
	releases: ★发布版,经过测试的代码
	snapshots: ★快照版,内部使用的测试版仓库
    Central: ★中央仓库下载的jar包
```

④virtual(虚拟)：兼容Maven1版本的jar或者插件

![1559551723693](.\img\图片21.png)

nexus仓库类型与安装目录对应关系

![1559551752012](.\img\图片22.png)

### 5.3 将项目发布到maven私服

maven私服是搭建在公司局域网内的maven仓库，公司内的所有开发团队都可以使用。例如技术研发团队开发了一个基础组件，就可以将这个基础组件打成jar包发布到私服，其他团队成员就可以从私服下载这个jar包到本地仓库并在项目中使用。

将项目发布到maven私服操作步骤如下：

1. ==配置maven的settings.xml文件==（修改前请先备份）

```xml
在<servers></servers>标签中添加

<server>
    <id>releases</id>
    <username>admin</username>   
    <password>admin123</password>
</server>
<server>
    <id>snapshots</id>
    <username>admin</username>
    <password>admin123</password>
</server>
```

​      注意：一定要在idea工具中引入的maven的settings.xml文件中配置 

2. 配置项目的pom.xml文件

```xml
想将哪个项目打成包发布到私服上,就在那个项目的pom.xml文件中添加相关配置
<distributionManagement>
    <repository>
       <id>releases</id>
       <url>http://localhost:8081/nexus/content/repositories/releases/</url>
    </repository>
    <snapshotRepository>
       <id>snapshots</id>               <url>http://localhost:8081/nexus/content/repositories/snapshots/</url>    </snapshotRepository>
</distributionManagement>
```

3. 执行mvn deploy命令（第一次需要联网）

![1600900695200](/img/1600900695200.png)

运行后的结果：

私服仓库

![1600900750039](/img/1600900750039.png)

所对应的本地文件

![1600900817382](/img/1600900817382.png)

### 5.4 从私服下载jar到本地仓库

前面我们已经完成了将本地项目打成jar包发布到maven私服，下面我们就需要从maven私服下载jar包到本地仓库。

具体操作步骤如下：

在maven的settings.xml文件中配置下载模板

```xml
在<profiles></profiles>标签体中添加如下配置
<profile>
	<id>dev</id>
	<repositories>
		<repository>
			<id>nexus</id>
			<!--仓库地址，即nexus仓库组的地址-->
			<url>http://localhost:8081/nexus/content/groups/public/</url>
			<!--是否下载releases构件-->
			<releases>
			<enabled>true</enabled>
			</releases>
			<!--是否下载snapshots构件-->
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
	</repositories>
	<pluginRepositories>
		<!-- 插件仓库，maven的运行依赖插件，也需要从私服下载插件 -->
		<pluginRepository>
			<id>public</id>
			<name>Public Repositories</name>
			<url>http://localhost:8081/nexus/content/groups/public/</url>
		</pluginRepository>
	</pluginRepositories>
</profile>
```

在maven的settings.xml文件中配置激活下载模板

```xml
<activeProfiles>
	<activeProfile>dev</activeProfile>
</activeProfiles>
```

### 小结

```java
安装私服:
	以超级管理员身份 cmd 执行安装命令
    启动: 
	通过浏览器访问nexus服务器:
		http://localhost:8081/nexus
		用户名: admin
        密码: admin123
   	仓库分类:
		releases: ★存放我们发布版代码,经过测试的代码
		snapshots: ★存放我们快照版代码,内部使用的测试版仓库
    	Central: ★中央仓库下载的jar包
	----------------------------
    将我们的项目打包安装到私服中:
		1.在setting.xml文件中配置server
            <server>
                <id>releases</id>
                <username>admin</username>   
                <password>admin123</password>
            </server>
            <server>
                <id>snapshots</id>
                <username>admin</username>
                <password>admin123</password>
            </server>
       2.在需要打包并发布的模块中 配置
            <!-- 配置私服的路径和登录信息 -->
    <distributionManagement>
        <repository>
            <id>releases</id>            		<url>http://localhost:8081/nexus/content/repositories/releases/</url>
        </repository>
        <snapshotRepository>
            <id>snapshots</id>
        <url>http://localhost:8081/nexus/content/repositories/snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
 ================================================
	从私服下载jar包:
		当本地没有jar包时,从我们的私服中下载jar包到本地
        如果私服中也没有jar包,私服会自动找中央仓库
        ----------------
        在setting.xml文件中,配置私服模板(复制),并激活
```







## 6. 将第三方jar安装到本地仓库和maven私服(了解)

在maven工程的pom.xml文件中配置某个jar包的坐标后，如果本地的maven仓库不存在这个jar包，maven工具会自动到配置的maven私服下载，如果私服中也不存在，maven私服就会从maven中央仓库进行下载。

但是并不是所有的jar包都可以从中央仓库下载到，比如常用的Oracle数据库驱动的jar包在中央仓库就不存在。此时需要到Oracle的官网下载驱动jar包，然后将此jar包通过maven命令安装到我们本地的maven仓库或者maven私服中，这样在maven项目中就可以使用maven坐标引用到此jar包了。

### 6.1 将第三方jar安装到本地仓库

①下载Oracle的jar包（略）在今天下发资料提供，无需下载

②mvn install命令进行安装

```java
mvn install:install-file -Dfile=ojdbc14-10.2.0.4.0.jar -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=10.2.0.4.0 -Dpackaging=jar（不需要记忆，用多了就记住了）
```

命令解释：

```yacas
install:install-file     --固定写法
-Dfile=F:\SmartWeb\opc-ua-stack-1.4.0-218.jar    --本地jar目录
-DgroupId=org.opcfoundation.ua    --一般为包名，也就是域名的反写
-DartifactId=opc-ua-stack         --jar的项目名称
-Dversion=1.4.0-218               --版本
-Dpackaging=Jar                   --类型，一般java项目为jar，web项目打包后为war
```



③查看本地maven仓库，确认安装是否成功

![1559552325997](.\img\图片24.png)

### 6.2 将第三方jar安装到maven私服

①下载Oracle的jar包（略）在今天下发资料提供，无需下载

②在maven的settings.xml配置文件中配置第三方仓库的server信息

```xml
<server>
  <id>thirdparty</id>
  <username>admin</username>
  <password>admin123</password>
</server>
```

③执行mvn deploy命令进行安装

```java
mvn deploy:deploy-file -Dfile=ojdbc14-10.2.0.4.0.jar -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=10.2.0.4.0 -Dpackaging=jar -Durl=http://localhost:8081/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty
```







