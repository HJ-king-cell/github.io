
# JSP&MVC

**今日目标**

```markdown
* jsp
	工作原理（servlet）
	脚本注释
	指令
	内置对象
	
* mvc模式介绍
```



# 一 JSP

## 1.1 概述

​	在很多动态网页中，绝大部分内容都是固定不变的，只有局部内容需要动态产生和改变。 为了弥补Servlet的缺陷(可以写页面,但是很麻烦)，SUN公司在Servlet的基础上推出了**JSP（Java Server Pages）**

- **Servlet缺陷：**




 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598751129651.png" alt="Screenshot of coverpage" title="Cover page">
</figure>


​	 JSP是简化Servlet编写页面的一种技术，它将Java代码和HTML语句混合在同一个文件中编写，页面动态资源使用java代码，页面静态资源使用html标签。

**简单来说：可以在html标签中嵌套java代码**

**作用：简化书写，展示动态页面**



## 1.2 快速入门

感知 : jsp 是 html上写java

​	在jsp页面，动态展示当前时间

```jsp
<%@ page import="java.util.Date" %>
<%@ page import="java.time.LocalDateTime" %>
<%@ page import="java.time.format.DateTimeFormatter" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>showtime</title>
  </head>
  <body>

    <%

      LocalDateTime date = LocalDateTime.now();

      String format = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").format(date);

      out.print(format);

    %>

  </body>
</html>
```



## 1.3 工作原理

- **JSP本质上就是一个Servlet**

  ```markdown
  # JSP运行原理
  0. 查看流程
      a. 运行看tomcat日志输出 
              1). Using CATALINA_BASE:   "C:\Users\spy\.IntelliJIdea2018.2\system\tomcat\_class111_web"
              2). 浏览器访问了jsp页面, 然后到这个目录下查看路径 work\Catalina\localhost\模块名\org\apache\jsp
              3). 找到xx_jsp.java(继承自org.apache.jasper.runtime.HttpJspBase) 和 对应的class文件
      b. 查找HttpJspBase(实现自HttpServlet)
        
  1. JSP运行流程：
  		1). 前端请求JSP页面
  		2). 服务器操作
  			1. JSP页面被tomcat翻译成Servlet,并且调用此Servlet的service方法
  			2. java代码: 得到 java 代码的运行结果
  			3. HTML代码：out.write() 输出在网页
  		3). 服务器响应 
  	
  2. JSP本质：是一个Servlet
  
  # 分析
  1. JSP页面被tomcat翻译成Servlet,怎么翻译的?
  	1). tomcat中有一个Servlet: JspServlet
  		a. 启动加载
  		b. 虚拟路径: *.jsp (只要访问后缀名为jsp的资源,都会被访问)
  	2). 用户用浏览器访问 xxx.jsp
      3). JspServlet会先运行, 将xxx.jsp 翻译成 xxx.java(Servlet)
      4). xxxServlet的service方法就会运行
      
  2. 怎么证明xxx.java是 Servlet呢?
  	1). 因为xxx.java 继承自org.apache.jasper.runtime.HttpJspBase
  	2). HttpJspBase 又继承自 HttpServlet
  ```



- HttpJspBase 类的继承关系图



 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598751958636.png" alt="Screenshot of coverpage" title="Cover page">
</figure>



- JSP运行原理




 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598754392014.png" alt="Screenshot of coverpage" title="Cover page">
</figure>



## 1.4 脚本和注释

### 1.4.1 脚本

**JSP通过脚本方式来定义java代码**

```markdown
1. <% 代码 %>
		脚本片段,生成在service方法中,每次请求的时候都会执行
		
2. <%! 代码 %>
		声明片段,在java代码中声明成员,放在jsp生成java文件中的成员位置

3. <%=代码 %>
		输出脚本片段,相当于out.print("代码") 方法，输出到jsp页面
		
4. <%@ 代码%>
		jsp中的命令：page include taglib
```

```jsp
<%--
  Created by IntelliJ IDEA.
  User: Simple
  Date: 2020/8/31
  Time: 9:52
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>script</title>
  </head>
  <body>

    <%--
    1. <% 代码 %>
		脚本片段,生成在service方法中,每次请求的时候都会执行

    2. <%! 代码 %>
            声明片段,在java代码中声明成员,放在jsp生成java文件中的成员位置

    3. <%=代码 %>
            输出脚本片段,相当于out.print("代码") 方法，输出到jsp页面

    --%>

    <%--代码段--脚本片段--%>
      <%--
          脚本片段在_jspService中编写
      --%>
    <%
      //Java代码
      //out--response.getWriter();
      out.print("我被脚本语言所显示");
      say();
      out.print(num);
    %>

    <%--
    输出脚本 --jsp文件中在—_jspService方法中使用out.print来显示
    --%>
    <%="我被输出脚本语言所显示"%>


    <%--
    快捷键--shift+ctrl+/
    选择内容--ctrl+w
    声明片段：里面的代码或作为当前Servlet类里的方法或属性
            里面不能使用_jspService中的声明对象
    --%>
    <%!
      int num = 1;
      public void say() {
        System.out.println("叽叽叽叽beibeibiebei");
      }

    %>


  </body>
</html>
```



### 1.4.2 注释

```markdown
1. html注释
		样式：<!-- 注释内容 -->
		作用：注释 html 代码
		显示：在浏览器页面中会显示出来
2. JSP注释
		样式：<%-- 注释内容 --%>
		作用：注释 java 代码
		显示：只在jsp文件中显示，不会浏览器页面中会显示出来
		
3. java注释（JSP脚本内使用）
		// 单行
		/* 多行 */
		/**文档 **/
#备注: 因为html注释在页面源码中可见, 所以在jsp中推荐使用JSP注释或java注释	
```



```JAVA
<%--
  Created by IntelliJ IDEA.
  User: Simple
  Date: 2020/8/31
  Time: 10:23
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>comment</title>
  </head>
  <body>

    <!--1.html注释
      特点：
        1.在html源码中会显示
        2.在jsp所对应的servlet文件中--out.write 来进行输出
    -->


    <%--2.jsp注释
      特点：
        1.不在html源码中会显示
        2.不在jsp所对应的servlet出现
     --%>


    <%
        //java的单行注释

      /*
        特点：
          1.不在html源码中会显示
          2.在jsp所对应的servlet出现，在_jspService中出现
       */
      /**
       * 文档注释
       */
        out.print("我是一个内容");
    %>







  </body>
</html>

```





## 1.5 三大指令

```markdown
* 作用
		JSP指令用来设置整个JSP页面相关的属性	
* 格式
		<%@ 指令名称 属性名1="属性值1" 属性名2="属性值2" ...%>
		
* 三大指令
	1. page：配置JSP页面
	
	2. include：页面包含（静态）
	
	3. taglib:导入资源文件
```

### 1.5.1 page指令

```markdown
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	1). contentType 等价于 response.setContentType(); 设置响应体的MIME类型和编码方式
	2). language    目前仅支持java语言
	3). import      导入jar包
		<%@ page import="java.util.Date" %>
	4). errorPage   当前页面报错后，跳转指定错误提示页面
	5). isErrorPage 声明当前jsp页面是一个异常处理页面，打开异常开关
		false（默认）
		true：可以操作exception异常对象
```

```jsp
<%@ page import="java.time.LocalDateTime" %><%--
  Created by IntelliJ IDEA.
  User: Simple
  Date: 2020/8/31
  Time: 10:34
  To change this template use File | Settings | File Templates.
--%>
<%--jsp都是以page 来开头的--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" errorPage="error.jsp" %>
<%--
	1). contentType 等价于 response.setContentType(); 设置响应体的MIME类型和编码方式
	2). language    目前仅支持java语言
	3). import      导入jar包
		<%@ page import="java.util.Date" %>
	4). errorPage   当前页面报错后，跳转指定错误提示页面
	5). isErrorPage 声明当前jsp页面是一个异常处理页面，打开异常开关
		false（默认）
		true：可以操作exception异常对象

--%>
<html>
  <head>
    <title>order</title>
  </head>
  <body>

    <%

      LocalDateTime.now();
      int i = 1 / 0;
    %>
  
  </body>
</html>

```

500.jsp（异常处理页面）

```jsp
<%--
  Created by IntelliJ IDEA.
  User: Simple
  Date: 2020/8/31
  Time: 10:52
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" isErrorPage="true" %>
<html>
  <head>
    <title>error</title>
  </head>
  <body>

    <%
      /*
        isErrorPage="true"
        jsp所对应的service中的_jspService方法中--Exception
       */
      String message = exception.getMessage();

      out.print("  你看到我，就说明有问题了:"+message);


    %>
  </body>
</html>

```







### 1.5.2 include指令（静态包含）

```markdown
1. 作用: 在一个jsp页面包含其他的页面
2. 目的: 避免代码冗余
3. 举例:
		a. 有A.jsp和B.jsp两个页面, 头部都一样, 那么两个页面得写相同的代码两遍, 很冗余
        b. 把头部抽取出来 top.jsp , 分别A.jsp和B.jsp包含即可, 这样避免了代码冗余
        
4. 语法:  
	<%@include file="top.jsp"%>        
```





 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1587436830957.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

top.jsp 

```jsp
<%--
  Created by IntelliJ IDEA.
  User: Simple
  Date: 2020/8/31
  Time: 11:14
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>heard</title>
  </head>
  <body>
      我是头
  </body>
</html>
```



```jsp
<%--
  Created by IntelliJ IDEA.
  User: Simple
  Date: 2020/8/31
  Time: 11:12
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>include</title>

    <style>

      div {
        border: 1px solid black;
        width: 100%;
        height: 200px;
      }

    </style>

  </head>
  <body>

    <div>
      <%@include file="heard.jsp"%>
    </div>
    <div>我是体</div>
    <div>我是尾</div>



  </body>
</html>

```





### 1.5.3 taglib指令

- 之后我们会讲解apache提供的jstl标准标签库（手动导入）

#### ① 导入jar包

 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1587437312125.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

#### ② 通过taglib指令引入


 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1587437472979.png" alt="Screenshot of coverpage" title="Cover page">
</figure>



## 1.6 九大内置对象

```markdown
* 作用
		在JSP页面中不需要获取和创建，可以直接使用的对象
		
* JSP一共有9个内置对象--面试
	变量名				  真实类型					作用
	pageContext		    PageContext			 	 当前页面中共享数据（域对象）
    request				HttpServletRequest       一次请求中共享数据（域对象）
    session             HttpSession				 一次会话中共享数据（域对象）
    application			ServletContext			 整个web应用共享数据（域对象）
    -----------------------------------------------
    response			HttpServletResponse		 响应对象
    page（this）				Object			    当前页面(servlet)对象
    out                 JSPWriter				 输出对象
    config              ServletConfig			 servlet配置对象
    exception           Throwable				 异常对象（默认关闭...）		

* 常用
	1. pageContext
			1）当前页面的域对象
			2）获取其他八个内置对象		
	2. request
			1）接收用户请求（参数）
			2）一次请求中域对象
			
	3. response
			1）设置响应
				字节流
				字符流
	4. out
			1）专门在jsp中处理字符输出流
				print(); // 在网页中输出内容
```



## 1.7 登录案例调整

完善之前的用户登录案例。



 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598768996016.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

示例代码：

```java
package com.itheima.web.servlet03;

import com.itheima.web.pojo.User;
import org.apache.commons.beanutils.BeanUtils;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.lang.reflect.InvocationTargetException;
import java.util.Map;

/**
 * <p></p>
 *
 * @Description:
 */
@WebServlet("/LoginServlet")
public class LoginServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        try {
            // 1.获得用户的数据，使用User对象来封装数据
            Map<String, String[]> parameterMap = request.getParameterMap();

            User user = new User();

            BeanUtils.populate(user,parameterMap);

            // 2.判断用户的验证码
            String code = (String) request.getSession().getAttribute("code_session");

            String input_code = request.getParameter("code");
            if (!(code.equalsIgnoreCase(input_code))) {
                request.setAttribute("err_msg","验证码错误");
                request.getRequestDispatcher("02login.jsp").forward(request,response);
                return;
            }


            // 3.判断用户的数据，用户数据是否存在--现实从数据库中查看
            // jack
            if ("jack".equalsIgnoreCase(user.getAccount()) && "123".equalsIgnoreCase(user.getPassword())) {

                // 用户数据存在，调到成功servlet
                // 3.1 将用户数据放到域对象中
                request.setAttribute("user", user);
                request.getRequestDispatcher("successServlet").forward(request, response);

            } else {
                // 3.用户数据不存在，调到失败servlet
                request.setAttribute("err_msg","用户密码不存在或错误");
                request.getRequestDispatcher("02login.jsp").forward(request,response);
            }

        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }


    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request,response);
    }
}
```



```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
        <h1>登录页面</h1>
  <!--
      相对当前路径:
          http://localhost:8080/day06-login

  -->
    <%
        // 1.获得域对象
        String err_msg = (String) request.getAttribute("err_msg");
        if (err_msg != null && err_msg.length() > 0) {
            out.print(err_msg);
        }
    %>
    <form action="LoginServlet" method="post">

        <input type="text" name="account" placeholder="请输入用户名"> <br>
        <input type="password" name="password" placeholder="请输入密码"> <br><br>
        <input type="text" name="code" placeholder="请输入验证码">
        <img src="CheckcodeServlet" alt="" id="codeImg"> <br>
        <input type="submit">

    </form>
    
    <script !src="">
        let img_element = document.querySelector("#codeImg");
        img_element.onclick = function () {
            //浏览器对修改的内容做判断，如果修改的内容没有变化，浏览器不做任何的操作
            //让当前的路径修改信息，修改具体路径后的可变内容
            let date = new Date();

            img_element.src = "CheckcodeServlet?time="+date.getTime();
        };

    </script>
</body>
</html>
```



# 二 MVC模式【思想】

## 2.1 JSP发展史

- 早期只有servlet，只能使用response输出html标签，非常麻烦。



 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598751129651.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

- 后来有了JSP(html+java)，简化了servlet开发；如果过度使用JSP，在JSP页面中写了大量的java代码和html标签，阅读性很差, 造成难于维护，难于分工协作的场景。



 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598769307252.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

- 再后来为了弥补过度使用jsp的问题，我们使用servlet+jsp这套组合拳，利于分工协作。



  <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598769726849.png" alt="Screenshot of coverpage" title="Cover page">
</figure>



## 2.2 MVC介绍

MVC设计模式： Model-View-Controller简写。

MVC是软件工程中的一种软件架构模式，它是一种**分离业务逻辑**与**显示界面**的设计方法。

简单来说：前辈们总结的一套设计经验，适合在各种软件开发领域，目的：高内聚，低耦合

```markdown
* M：model（模型） JavaBean（1.处理业务逻辑、2.封装实体）

* V：view（视图）  Jsp（展示数据）

* C：controller（控制器）Servlet（1.接收请求、2.调用模型、3.转发视图）

* 优缺点
	优点
		降低耦合性，方便维护和拓展，利于分工协作
	缺点
		使得项目架构变得复杂，对开发人员要求高
```


 <figure class="thumbnails">
    <img src="picture/jsp&mvc/1598783868420.png" alt="Screenshot of coverpage" title="Cover page">
</figure>