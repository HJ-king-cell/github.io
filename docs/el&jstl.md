
# EL&JSTL&三层架构

**今日目标**

```markdown
1. el表达式

2. jstl标签库

3. 三层架构（mvc升级版...）

4. 用户信息列表展示 大作业
```



# 一 EL

## 1.1 概述

表达式语言（Expression Language）

```text
lambda表达式
xml(xpath路径表达式) 
正则表达式  

表达式: 以简短的符号表示复杂的内容
```

**作用**：主要用来代替jsp中脚本的功能，简化对java代码的编写。 

**语法**：${表达式}





## 1.2 使用

### 1.2.1 获取域中的值  (重要!!)

EL表达式主要简化从域对象（4个域）中获取数据

**语法**

```markdown
* 标准（了解）
	1. ${pageScope.键名} 
			从page域中获取指定键名对应的值

	2. ${requestScope.键名} 
			从request域中获取指定键名对应的值

	3. ${sessionScope.键名} 
			从session域中获取指定键名对应的值

	4. ${applicationScope.键名} 
			从servletContext域中获取指定键名对应的值
		
* 简化（掌握）
	${键名}
		特点：默认从最小域开始找，找到后直接显示，不在继续寻找
```

**获得其他数据**

```markdown
1. 获取字符串
		${键名}
		
2. 获取对象（User）
		${键名.属性名}

3. 获取List（Array）集合
		${键名[索引]}

4. 获取Map集合
		${键名.key}
		${键名["key"]}
		
5. 补充
	el不会出现null和索引角标越界问题
```
示例代码：

```jsp

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
        <div>
            <%
                /*
                *   简化从域对象的取值操作:
                *
                *      0. 四大域对象
                *           pageContext <  request < session < servletContext
                *           当前jsp内可用   一次请求   一次会话     应用全局
                *
                *      EL表达式: 简化jsp中java代码编写
                *
                *           ${表达式}
                *
                *      1.  简化从域对象取值操作 _ java代码
                *
                *      2. 标准:
                *           ${pageScope.name属性值}   -> pageContext.getAttribute("name属性值")
                *            ${requestScope.name2}
                            ${sessionScope.name3}
                            ${applicationScope.name4}

                       3. 简略:

                           ${name属性值}
                           
                           如果有多个域对象具备相同name属性值, 作用域小的域对象优先级高
                *
                * */
                pageContext.setAttribute("name1","value1");
                request.setAttribute("name2","value2");
                session.setAttribute("name3","value3");
                application.setAttribute("name4","value4");

            %>
            <%

                String value = (String) request.getAttribute("name2");
                out.print(value);
            %>
            <br>
            ${name2}
            <br>

            ${pageScope.name1}
            ${requestScope.name2}
            ${sessionScope.name3}
            ${applicationScope.name4}
            <br>
            ${name1}
            ${name2}
            ${name3}
            ${name4}

            <hr>

            <%
//                pageContext.setAttribute("name","value1");
                request.setAttribute("name","value2");
                session.setAttribute("name","value3");
                application.setAttribute("name","value4");
            %>
            ${name}
        </div>
</body>
</html>

```



```jsp
<%@ page import="com.itheima01.el.User" %>
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.Collections" %>
<%@ page import="java.util.HashMap" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

        <div>
            <%--
            1. 获取字符串
                    ${键名}

            2. 获取对象的属性（User）
                    ${键名.属性名}

            3. 获取List（Array）集合中的元素
                    ${键名[索引]}

            4. 获取Map集合中的元素
                    ${键名.key}
                    ${键名["key"]}

            5. 补充
                el不会出现null和索引角标越界问题
            --%>
            <%
               request.setAttribute("str","abc");

                User user = new User(28, "张三");
                request.setAttribute("user",user);

                ArrayList<Object> list = new ArrayList<>();
                Collections.addAll(list,"a","b","c");
                request.setAttribute("list",list);

                HashMap<String, String> map = new HashMap<>();
                map.put("xx","yy");
                map.put("xx.a","zz");

                request.setAttribute("map",map);
            %>
            ${str} <br>
            ${user.id} , ${user.name} <br>
            ${list[1]} <br>
            ${map.xx} , ${map["xx.a"]}
        </div>

</body>
</html>

```



### 1.2.2 执行运算

```markdown
* 算数运算符
		语法： + 	- 	* 	/(div) 		%(mod)
		
* 比较运算符
		语法：> 	< 	>= 	<= 	==(eq) 		!=(ne)
		
* 逻辑运算符
		语法：&&(and) ||(or) !(not)
		
* 三元运算符
		语法：${条件表达式？为真:为假}

------------------------------

* 空运算符
 		语法：${empty 对象}
 		功能：
 			a. 可以判断对象是否为null--为空返回为true，不为空返回为false
 			b. 可以判断字符串是否空串 "" --为空返回true，不为空返回false
 			c. 可以判断一个集合的长度是否为0 --集合为空返回为true，集合不为空返回false

* 非空运算符
 		语法：${not empty 对象}
 		功能：
 			a. 可以判断对象是否为null--为空返回为false，不为空返回为true
 			b. 可以判断字符串是否空串 "" --为空返回false，不为空返回true
 			c. 可以判断一个集合的长度是否为0 --集合为空返回为false，集合不为空返回true
```



```jsp
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.List" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <div>
        <%
            int a = 10;
            int b = 4;
            request.setAttribute("a",a);
            request.setAttribute("b",b);

            String str = new String("abc");
            request.setAttribute("str",str);

            request.setAttribute("c","10");
        %>


        <%--
            算术运算符

        --%>
        ${a / b} , ${a div b} <br>
        <%--
            比较运算符: 比较的是字面值
        --%>
        ${str == "abc"} <br>
        ${a == c} <br>

        <%--
            三元运算符
        --%>
        ${a < b? "呵呵" : ""}

        <input type="checkbox" checked>
        <input type="checkbox" ${a > b? "checked" : ""}> 记住我 <br>

        <%--
            空运算
                语法：${not empty 对象}
                    不为空会返回true
                功能：
                    a. 可以判断对象是否为null
 			        b. 可以判断字符串是否空串 ""
 			        c. 可以判断一个集合的长度是否为0

 			        以下三种情况都是false
        --%>
        <%

            Object obj = null;
            String s = "";  // 空串
            List list = new ArrayList<>(); // 空集合

            request.setAttribute("obj",obj);
            request.setAttribute("s",s);
            request.setAttribute("list",list);
        %>
        ${not empty obj}
        ${not empty s}
        ${not empty list}
    </div>
</body>
</html>

```





### 1.2.3 隐式对象

```markdown
* el表达式中有11个隐式对象

	1.pageScope：代表page域，可以用来获取page域中的属性
	2.reqeustScope：代表reqeust域，可以用来获取reqeust域中的属性
	3.sessionScope：代表session域，可以用来获取session域中的属性
	4.applicationScope：代表application域，可以用来获取application域中的属性
	
	5.param 代表请求参数组成的map集合${param.userName}--request.getParameter(name)
    6.paramValues 代表请求参宿组成的map集合，但是此集合的value是String[]，用来获取一名多值的param
      request.getParameterValues(name)
    
    7.header 获取请求头组成的map  --request.getHeader(name)
    8.headerValues 获取请求头组成的map但是value是一个String[]，用来获取一名多值的head   
      request.getHeaders(name)
    
    9.cookie 获取cookie组成的map对象，此map的值是一个cookie对象${cookie.cookieName.cookieValue} 
    
    10.initParam 以map封装的web.xml中配置的整个web应用的初始化参数
       ServletContext.getInitParameter(name);
    
    11.pageContext:代表pageContext对象，注意和pageScope进行区分
    
    #需要掌握：
		1. pageContext
			就是jsp九大内置对象之一，可以获得其他八个内置对象
		2. cookie
			可以获取浏览器指定cookie名称的值
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
        <div>
            <h3>el隐式对象..</h3>
            <%--
                1. pageContext:
                    ${pageContext.request.contextPath} -> 动态获取项目虚拟路径
                2. cookie
                     ${cookie.cookie的name.value}  -> 某个name的cookie对应value
            --%>
            <%
                String contextPath = request.getContextPath();
                System.out.println(contextPath);
            %>
            ${pageContext.request.contextPath} <br>

            <a href="http://localhost:8080/day08-el&jstl">index.jsp</a> <br>
            <a href="http://localhost:8080${pageContext.request.contextPath}">index.jsp</a>
            <a href="index.jsp">index.jsp</a> <br>

            ${cookie.JSESSIONID.value}

        </div>
</body>
</html>
```





### 1.2.4 了解

```markdown
* el表达式的支持
		servlet2.3规范中，默认不支持el表达式

* 如果要忽略el表达式
	1）忽略当前jsp页面中所有的el表达式
		设置jsp中page指令中：isELIgnored="true" 属性
	2）忽略单个el表达式
		\${表达式}
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<%--
        isELIgnored="true"   (整个jsp)
            1. 如果是true : el表达式失效
            2. 如果是false : el表达式有效 (默认)

        \ el表达式: 忽略单个
--%>
<html>
<head>
    <title>Title</title>
</head>
<body>
        <div>
            <h3>el隐式对象..</h3>
            <%--
                1. pageContext:
                    ${pageContext.request.contextPath} -> 动态获取项目虚拟路径
                2. cookie

                     ${cookie.cookie的name.value}  -> 某个name的cookie对应value

            --%>
            <%
                String contextPath = request.getContextPath();
                System.out.println(contextPath);
            %>
            ${pageContext.request.contextPath} <br>

            <a href="http://localhost:8080/day08-el&jstl">index.jsp</a> <br>
            <a href="http://localhost:8080${pageContext.request.contextPath}">index.jsp</a>
            <a href="index.jsp">index.jsp</a> <br>

            \${cookie.JSESSIONID.value}

        </div>
</body>

<script>
    // es6: 模板字符串(兼容)
    let  money = "100"
    let str = `一共\${money}元`
    document.write(str)
</script>
</html>

```







## 1.3 JavaBean

所以的javaBean指的是符合特定规范的java标准类

**使用规范**

1. 所有字段（成员变量）为private     (最好用引用类型,比如int -> Integer)
2. 提供public无参构造方法
3. 提供getter、setter的方法
4. 实现serializable接口



例如：下面User类，有四个字段（成员变量），有无参构造方法，有一个属性（username）

```java
public class User implements Serializable {

    private String username;

    private Integer age;

    private String gender;

    public User() {
    }



    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

}
```





# 二 JSTL

## 2.1 概述

Jsp 标准标签库（Jsp Standard Tag Library），是由Apache组织提供的开源的jsp标签库

作用：替换和简化jsp页面中java代码的编写

> 问题: jsp过度使用,html和java代码混杂,不利于阅读和长期维护
>
> 书写: html + java  -> html标签     
>
> 解决: 标签库(用标签代替java代码书写)  
>
> 写起来像html,本质还是servlet

JSTL标准标签库有5个子库，但随着发展，目前常使用的是它的核心库（Core）

| **标签库**          | **标签库的URI**                        | **前缀** |
| ------------------- | -------------------------------------- | -------- |
| **Core**            | http://java.sun.com/jsp/jstl/core      | c        |
| 国际化(几乎不用)    | http://java.sun.com/jsp/jstl/fmt       | fmt      |
| SQL(过时)           | http://java.sun.com/jsp/jstl/sql       | sql      |
| XML(过时)           | http://java.sun.com/jsp/jstl/xml       | x        |
| Functions(几乎不用) | http://java.sun.com/jsp/jstl/functions | fn       |



## 2.2 Core标签使用

### 2.2.1 使用步骤

#### ① 当前web项目引入jar包

![1587523977994](assets/1587523977994.png) 

#### ② 当前jsp页面tablib指令引入

![1587524031438](assets/1587524031438.png) 

### 2.2.2 常用标签

#### ① c:if标签

```markdown
* 相当于，java中 if(){}
	语法
		<c:if test="boolean值"></c:if>
			true：显示标签体内容
			false：隐藏标签体内容
		通常与el表达式一起使用
	注意：此标签没有else功能，如果想实现else效果，请让条件取反
```

```jsp
<%@ page import="cn.itcast.domain.User" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>demo1</title>
</head>
<body>
 <div>

        <%--
            c:if 标签
               1.  必须要写的属性: test
               2.  test = el表达式

        --%>
        <%
            int n = 10;
            request.setAttribute("n",n);
        %>
        <%
            if(n > 5){
                out.print("呵呵");
            }
        %>
            <br>
        <c:if test="${n > 5}">
            呵呵
        </c:if>
    </div>
</body>
</html>
```







#### ② c:forEach标签  

```markdown
* 相当于java中的for循环
	1）普通for
		for(int i=1; i<=5; i++){
            i
		}
		<c:forEach begin="1" end="5" step="1" var="i">
			${i}
		</c:forEach>
			begin="1" 起始值（包含）
			end="5"   结束值（包含）
			step="1"  步长为1
			var="i"   当前输出临时变量
	2）增强for
		for(User user : list){
            user
		}
		<c:forEach items="${list}" var="user" varStatus="s">
			${user}
		</c:forEach>
			items="list" 集合
			var="user"   当前输出的临时变量
			varStatus="s" 变量状态
				index 当前索引 从0开始
				count 计数器   从1开始
```

```jsp
<%@ page import="java.util.ArrayList" %>
<%@ page import="java.util.Collections" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<%--
    使用jstl标签库
    1. 导包: 两个
    2. taglib指令声明
            prefix="c"  前缀

--%>
<html>
<head>
    <title>Title</title>
</head>
<body>
  
    <hr>
    <div>
        <%--
             c:foreach 标签  -> 普通for循环
                1. var : 变量
                    var = "i" -> pageContext.setAttribute("i",i)
                      ${i}

                2. begin : 遍历初始值
                3. end : 遍历结束值
                4. step : 步进(默认为1)
        --%>
        <%
            for (int i = 0; i <= 9 ; i+=2) {
                out.println(i);
            }
        %>
            <br>
        <c:forEach var="i" begin="0" end="9" step="2">
            ${i}
        </c:forEach>
            <br>
        <%--
             c:foreach 标签  -> 增强for循环
                1. var : 变量
                    自动存到域对象,需要用el表达式获取
                2. items : 被遍历的对象
                    经常写el表达式
        --%>
        <%
            ArrayList<String> list = new ArrayList<>();
            Collections.addAll(list,"a","b","c","d");

            request.setAttribute("list",list);

        %>
        <%
            for (String element : list) {

            }
        %>
        <c:forEach var="element" items="${list}">
            ${element}
        </c:forEach>

    </div>
</body>
</html>

```



# 三 三层架构（MVC升级版）

## 3.1 概述

通常意义上的三层架构就是将整个业务应用划分为：表示（现）层、业务逻辑层、数据访问层。

区分层次的目的 为了**高内聚低耦合**的思想

> 表示（现）层：又称为web层，与浏览器进行数据交互（控制器和视图）
>
> 业务逻辑层：又称为service层，处理业务数据（if判断，for循环）
>
> 数据访问（持久）层：又称为dao层，与数据库进行交互的（每一条（行）记录与javaBean实体对应）

**包目录结构**

```markdown
* com.itheima 基本包（公司域名倒写）

* com.itheima.dao 持久层

* com.itheima.service 业务层

* ccom.itheima.web 表示层

* com.itheima.domain 实体（JavaBean）
			 entity
* com.itheima.util 工具
```



 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598786179375.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

## 3.2 案例：用户信息列表展示

**需求**

使用MVC模式升级版（三层架构）开发代码，完成用户显示列表功能。 



### 3.2.1 需求分析

![1587536245514](assets/1587536245514.png) 
 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1587536245514.png" alt="Screenshot of coverpage" title="Cover page">
</figure>


### 3.2.2 环境搭建

```markdown
# 开发环境
1. 系统  windows开发环境
2. 软件: idea  	mysql5.6 	tomcat8.5.31...
3. 做开发
	a. 需求分析: 项目负责人
	b. 技术选型: 组内讨论
		jsp(jstl包) 
```



#### ① 创建web项目



 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598789452814.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

#### ② 导入第三方依赖jar包（jstl）


 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598789430295.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

#### ③ 创建三层包目录结构



 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598789351847.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

#### ④ 最终项目结构


 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598789499633.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

### 3.2.3 代码实现

#### ① index.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>

        <a href="FindAllServlet">查看所有的好友</a>
  </body>
</html>
```



#### ② 实体类

```java
package com.itheima.cast.domian;

import java.io.Serializable;

/**
 * <p></p>
 *
 * @Description:
 */
public class Friend implements Serializable {

    private Integer id;
    private String name;
    private Integer age;
    private String address;
    private String email;

    public Friend() {
    }

    public Friend(Integer id, String name, Integer age, String address, String email) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.address = address;
        this.email = email;
    }

    @Override
    public String toString() {
        return "Friend{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", address='" + address + '\'' +
                ", email='" + email + '\'' +
                '}';
    }



    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}

```





#### ③ FindAllServlet

```java
package com.itheima.web;

import com.itheima.pojo.Friend;
import com.itheima.service.FriendService;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;

@WebServlet("/FindAllServlet")
public class FindAllServlet extends HttpServlet {

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //1. 接收请求
        //2. 业务处理
        FriendService service  = new FriendService();
        List<Friend> list = service.findAll();

        System.out.println(list);
        //3. 数据转发到jsp
        request.setAttribute("list",list);
        //TODO: list.jsp待会写
        request.getRequestDispatcher("list.jsp").forward(request,response);
    }

}
```



#### ④ Service

```java
package com.itheima.service;

import com.itheima.dao.FriendDao;
import com.itheima.pojo.Friend;

import java.util.List;
// 业务层: 业务处理
public class FriendService {

    public List<Friend> findAll() {
        //查询数据库,获取所有的好友
        FriendDao dao = new FriendDao();
        List<Friend> list = dao.findAllFriend();

        return list;
    }
}

```



#### ⑤ Dao

```java
package com.itheima.dao;

import com.itheima.pojo.Friend;

import java.util.ArrayList;
import java.util.List;

public class FriendDao {

    public List<Friend> findAllFriend() {
        //伪数据
        ArrayList<Friend> list = new ArrayList<>();
        list.add(new Friend(1,"猪八戒1","男",25,"广东","834523234","zhuzhuxia@itcast.cn"));
        list.add(new Friend(2,"猪八戒2","男",25,"广东","834523234","zhuzhuxia@itcast.cn"));
        list.add(new Friend(3,"猪八戒3","男",25,"广东","834523234","zhuzhuxia@itcast.cn"));
        list.add(new Friend(4,"猪八戒4","男",25,"广东","834523234","zhuzhuxia@itcast.cn"));
        list.add(new Friend(5,"猪八戒5","男",25,"广东","834523234","zhuzhuxia@itcast.cn"));
        list.add(new Friend(6,"猪八戒6","男",25,"广东","834523234","zhuzhuxia@itcast.cn"));

        return list;
    }
}

```





#### ⑥ list.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

    <table border="1px" cellspacing="0" cellpadding="10px" width="800px">
       <tr>
           <th>id</th>
           <th>name</th>
           <th>sex</th>
           <th>age</th>
           <th>address</th>
           <th>qq</th>
           <th>email</th>
       </tr>
        <c:forEach var="element" items="${list}">
            <tr>
                <td>${element.id}</td>
                <td>${element.name}</td>
                <td>${element.sex}</td>
                <td>${element.age}</td>
                <td>${element.address}</td>
                <td>${element.qq}</td>
                <td>${element.email}</td>
            </tr>
        </c:forEach>

    </table>
</body>
</html>

```

 