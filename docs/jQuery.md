# jQuery框架

## 学习目标

1. 选择器

   1). <font color="red">能够使用jQuery的基本选择器</font>

   2). 能够使用jQuery的层级选择器
2. DOM操作

   1). <font color="red">能够使用jQuery的DOM操作的方法</font>

3. 案例

   1). 能够完成案例: 隔行换色

   2). 能够完成案例：商品全选



## 一. jQuery概述

### 1.1 jquery是什么

jQuery是一个JavaScript的开发库

### 1.2 为什么要使用Jquery开发

好处：提高开发效率，降低开发难度，降低开发成本。jQuery是一个免费开发库。

jQuery开发库也是使用JavaScript开发出来，本质上jQuery开发库就是JS代码。





 <figure class="thumbnails">
    <img src="picture/Ajax&json/1553152579233.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

#### jQuery库特点：

1. 轻量级：框架本身很小，占用资源少。

2. 
   兼容性：可以运行在所有主流的浏览器上。

3. 插件：本身还支持大量的插件

4. 宗旨：write less do more

### 1.3 自定义JS库

------

​	库（library）是完成某种功能的半成品，抽取重复繁琐的代码，提供简洁强大的方法实现。我们可以自定义一个js框架,体会框架给开发带来的便捷之处.



**cusjs.js**

```js
/*
方法的封装
 */
// function jQuery(id) {
//     var div_e = document.querySelector(id);
//     return div_e;
// }

/*
    js的特殊变量：$--在js中可以声明一个变量

        Jquery使用时，$()来获得内容的（Jquery对象）

 */
function $(id) {
    var div_e = document.querySelector(id);
    return div_e;
}
```



**引入外部js文件**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>自定义js框架</title>

</head>
<body>
<div id="myDiv">世界上最遥远的距离不是生与死，而是你亲手制造的BUG就在你眼前，你却怎么都找不到她。</div>

    <script src="../js/cusjs.js"></script>
    <script !src="">

        /*
        方法的封装
         */
        // function jQuery(id) {
        //     var div_e = document.querySelector(id);
        //     return div_e;
        // }

        /*
            js的特殊变量：$--在js中可以声明一个变量

                Jquery使用时，$()来获得内容的（Jquery对象）

         */
        // function $(id) {
        //     var div_e = document.querySelector(id);
        //     return div_e;
        // }
    </script>

    <script !src="">

        // 1.获得id为myDiv标签中的text数据
        // var div_e = document.querySelector("#myDiv");
        //
        // console.log(div_e.innerText);
        var div_e = $("#myDiv");//  ${} --- $()
        console.log(div_e.innerText);

    </script>


</body>
</html>
```



## 二.  jQuery的导入与使用

### 2.1 官网：www.jquery.com



 <figure class="thumbnails">
    <img src="picture/Ajax&json/1553152806464.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

中文文档：https://www.jquery123.com/



版本的选择：

1. 标准版：jquery-3.3.1.js 通常用于开发，可读性好。

2. 压缩版：jquery-3.3.1.min.js 用于项目实际运行环境中，体积小。(minimum:  生产版)

   > 变量命名能用一个字母,坚决不写两个,能不用空格注释就不用....  最小化

### 2.2 案例：导入jQuery并测试是否成功

**步骤**

1. 准备jquery框架，复制到项目中js目录下
2. 在HTML中使用script标签导入jquery.js文件就可以使用了

**代码**

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>01-HTML引入Jquery</title>

</head>
<body>
<div id="myDiv">世界上最远的距离，是我在if里你在else里，虽然经常一起出现，但却永不结伴执行。</div>
</body>

<!--    1.引入jQuery的js库
            注意事项：引入的js库要做js代码前引入，不然里面的方法不识别
-->
    <script src="../js/jquery-3.3.1.js"></script>

    <!--2. 编写js代码，代码要在引入jquery文件之后-->
    <script !src="">

        var text = $("#myDiv").text();
        console.log(text);

    </script>



</html>
```

**导入失败的情况**


 <figure class="thumbnails">
    <img src="picture/Ajax&json/1565228888062.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

## 三. jQuery和js的区别

#### 3.1 JS对象与jQuery对象

- JS对象：通过以前的JS代码得到的对象，如：document.getElementById()，只能使用JS中方法和属性。

- jQuery对象：通过JQ选择器得到的对象，可以使用JQ中提供的方法。JQ对象本质上是一个JS数组。

####  3.2 为什么要转换

​	JS对象只能使用以前JS的方法和属性，不能使用JQ对象的方法。JQ对象中有很多功能强大方法，如果JS对象要调用JQ对象，就必须将JS对象转换成JQ对象。反之亦然。

####  3.3 转换语法

| **操作**                   | **方法**                   |
| -------------------------- | -------------------------- |
| **将JS对象-->jQuery对象**  | $(JS对象)                  |
| **将jQuery对象--> JS对象** | JQ对象[0] 或 JQ对象.get(0) |

#### 3.4 JS与JQ转换的演示：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>02-jq与js对象相互转换</title>
    <script src="../js/jquery-3.3.1.min.js"></script>
</head>
<body>
<div id="myDiv">通过不同方式获得文本内容</div>
<div >通过不同方式获得文本内容</div>

<script>
    // 通过js方式修改文本内容
    // var div_js = document.querySelector("#myDiv");
    // div_js.innerHTML = "js对象修改信息内容";

    // 通过jq方式修改文本内容
    // $("#myDiv").html("jquery对象修改信息内容");


    // js对象和jq对象的：属性和方法不同通用
    // 1.js使用jquer的方法---js对象不能直接使用jquery定义的方法
    // var div_js = document.querySelector("#myDiv");
    // div_js.html("jquery对象修改信息内容");

    // 2.jquery使用js对象中的方法---jquery对象不能直接使用js定义的方法
    // $("#myDiv").innerHTML = "js对象修改信息内容";


    // document.querySelector("#myDiv");---获得的js对象
    // $("#myDiv")  --Jquery对象

    // 3.使用js对象来转换为jquery对象并调用jquery方法
    // var div_js = document.querySelector("#myDiv");
    // $(div_js).html("js转jquery对象，并调用jquery对象修改信息内容");  //js对象转为jquery对象

    // 4.使用jquery对象来转换为js对象并调用js方法
    // jQuery的选择器获得的对象内容都是可以当为数组来处理
    // $("div").css("backgroundColor", "red");
    // 方式一：jq对象来转换为js对象
    // $("div").get(0).style.backgroundColor = "red" //通过jq对象来转换为js对象
    // 方式二：jq对象来转换为js对象
    $("div")[0].style.backgroundColor = "blue";

</script>
</body>
</html>
```

### 

## 四. Jquery绑定事件

jQuery的事件与js的事件的功能和作用一样，只是在使用语法上稍微有些差异。

```js
js对象.事件属性=function(){}

jq对象.事件函数(function(){})
```

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>13-jq事件绑定</title>
    <script src="../js/jquery-3.3.1.min.js"></script>
</head>
<body>
<input type="button" value="js方式" id="jsBtn"> <br>
<input type="button" value="jq方式" id="jqBtn"> <br>



<form id="myForm" action="http://www.baidu.com" method="get"  >
<!--     账号： <input name="account" onblur="inputBlur()" > <br>-->
     账号： <input name="account" > <br>
      密码：<input name="password"> <br>
        省份：<select name="province" >
              <option value="bj">北京</option>
              <option value="sh">上海</option>
              <option value="heib">河北</option>
            </select><br>
      <input type="submit" value="提交"  />
    </form>




</body>
    <script src="../js/jquery-3.3.1.js"></script>




    <script !src="">
        // 提交事件
        // 1.js对象绑定事件
        // var form_js = document.querySelector("#myForm");
        //
        // form_js.onsubmit = function () {
        //     console.log("表单提交事件触发");
        //     // return false; --阻止表单提交
        //     return true;//不阻止表单提交
        // }

        // 2.jq对象事件绑定
        $("#myForm").submit(function () {
            console.log("表单提交事件触发");
            // return false; //--阻止表单提交
            return true;//不阻止表单提交
        })


    </script>


    <script !src="">

        // 点击事件

        // 1.js对象绑定事件

        // 将事件方法提取出来（回调函数）-- 方法在js中时对象
        function btn_click() {
            console.log("点击事件");
        }

        var btn_js = document.querySelector("#jsBtn");

        // btn_js.onclick=function () {
        //     console.log("js 点击事件");
        // }

        btn_js.onclick = btn_click;


        // 2.jq对象的时间绑定
        // $("#jqBtn").click(function () {
        //     console.log("jq 点击事件")
        // })

        $("#jqBtn").click(btn_click)

    </script>

</html>
```



**常见事件**

```markdown
1. 点击事件：
        1. click()：单击事件  （重要）
        2. dblclick()：双击事件
2. 焦点事件
        1. blur()：失去焦点 （重要）
        2. focus():元素获得焦点。

3. 鼠标事件：
        1. mousedown()	鼠标按钮被按下。
        2. mouseup()	鼠标按键被松开。
        3. mousemove()	鼠标被移动。   （重要）
        4. mouseover()	鼠标移到某元素之上。
        5. mouseout()	鼠标从某元素移开。 （重要）
        
4. 键盘事件：
		1. keydown()	某个键盘按键被按下。	
		2. keyup()		某个键盘按键被松开。
		3. keypress()	某个键盘按键被按下并松开。

5. 改变事件
        1. change()	域的内容被改变。（重要）

6. 表单事件：
        1. submit()	提交按钮被点击。（重要）
```





## 五. Jquery选择器

**选择器的作用**

​	如果要操作网页中各种元素，首先要选中这些元素。jQuery中的选择器功能强大，选择方式灵活多样。

**jQuery常用的选择器有如下4类：**

1. 基本选择器
2. 层级选择器
3. 属性选择器
4. 基本过滤选择器

### 4.1 选择器：基本选择器

**基本选择器的语法**

| **基本选择器** | **语法**    |
| -------------- | ----------- |
| **ID选择器**   | $("#ID")    |
| **类选择器**   | $(".类名")  |
| **标签选择器** | $("标签名") |

**示例：基本选择器的使用**

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
 <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
 <title>01-基本选择器.html</title>
 <!--   引入jQuery --> 
 <script src="../js/jquery-3.3.1.js" type="text/javascript"></script>
 <link rel="stylesheet" type="text/css" href="./css/style.css" /> 
 <script type="text/javascript">
  </script>
</head>
<body>
  <button id="reset">手动重置页面元素</button>
  <input type="checkbox" id="isreset" checked="checked"/><label for="isreset">点击下列按钮时先自动重置页面</label><br /><br />
 <h3>基本选择器.</h3>
 
 <!-- 控制按钮 -->
  <input type="button" value="选择 id为 one 的元素." id="btn1"/>  
  <input type="button" value="选择 class 为 mini 的所有元素." id="btn2"/>
  <input type="button" value="选择 元素名是 div 的所有元素." id="btn3"/>

  <br /><br />

   <!-- 测试的元素 -->
  <div class="one" id="one" >
 id为one,class为one的div
      <div class="mini">class为mini</div>
  </div>

    <div class="one"  id="two" title="test" >
	 id为two,class为one,title为test的div.
      <div class="mini"  title="other">class为mini,title为other</div>
      <div class="mini"  title="test">class为mini,title为test</div>
  </div>

  <div class="one">
      <div class="mini">class为mini</div>
      <div class="mini">class为mini</div>
	  <div class="mini">class为mini</div>
	  <div class="mini"></div>
  </div>



  <div class="one">
      <div class="mini">class为mini</div>
      <div class="mini">class为mini</div>
	  <div class="mini">class为mini</div>
	  <div class="mini"  title="tesst">class为mini,title为tesst</div>
  </div>


  <div style="display:none;"  class="none">style的display为"none"的div</div>
  
  <div class="hide">class为"hide"的div</div>

  <div>
  包含input的type为"hidden"的div<input type="hidden" size="8"/>
  </div>

    <script src="../js/jquery-3.3.1.js"></script>

    <script !src="">

        // 1.id选择器
        // $("#id")
        $("#btn1").click(function () {
            $("#one").css("backgroundColor", "red");
        })

        // 2.类选择器
        // $(".类选择器")
        $("#btn2").click(function () {
            $(".mini").css("backgroundColor", "red");
        })


        // 3.标签选择器
        // $("标签名")
        $("#btn3").click(function () {
            $("div").css("backgroundColor", "red");
        })
    </script>


</body>
</html>
```

### 

### 4.2 选择器：层级选择器

| **层级选择器**                                          | **语法**          |
| ------------------------------------------------------- | ----------------- |
| 获取A元素内部所有B元素，B元素是A元素的子孙元素 (多层级) | A B               |
| 获得A元素下面的所有B子元素 (单层级)                     | 父选择器>子选择器 |

**演示案例**

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
 <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
 <title>02-层次选择器.html</title>
 <!--   引入jQuery --> 
 <script src="../js/jquery-3.3.1.js" type="text/javascript"></script>
 <link rel="stylesheet" type="text/css" href="./css/style.css" />  
  <script type="text/javascript">
  </script>
</head>
<body>
  <h3>层次选择器.</h3>
  <button id="reset">手动重置页面元素</button>
  <input type="checkbox" id="isreset" checked="checked"/><label for="isreset">点击下列按钮时先自动重置页面</label><br /><br />
  <h3>常见选择器</h3>
  <input type="button" value="选择 body内的所有div元素." id="btn1"/>
  <input type="button" value="在body内,选择子元素是div的。" id="btn2"/>

  <br />
  <br />
  
   <!-- 测试的元素 -->
  <div class="one" id="one" >
 id为one,class为one的div
      <div class="mini">class为mini</div>
  </div>

    <div class="one"  id="two" title="test" >
	 id为two,class为one,title为test的div.
      <div class="mini"  title="other">class为mini,title为other</div>
      <div class="mini"  title="test">class为mini,title为test</div>
  </div>

  <div class="one">
      <div class="mini">class为mini</div>
      <div class="mini">class为mini</div>
	  <div class="mini">class为mini</div>
	  <div class="mini"></div>
  </div>

  

  <div class="one">
      <div class="mini">class为mini</div>
      <div class="mini">class为mini</div>
	  <div class="mini">class为mini</div>
	  <div class="mini"  title="tesst">class为mini,title为tesst</div>
  </div>


  <div style="display:none;"  class="none">style的display为"none"的div</div>
  
  <div class="hide">class为"hide"的div</div>
 
  <div>
  包含input的type为"hidden"的div<input type="hidden" size="8"/>
  </div>



</body>
<script>
    /*
    * 层级
            ancestor descendant  (多层级)
                祖先  后代
            parent > child  (单层级)
                父母  子女

    * */
    $("#btn1").click(function () {
        $("body div").css("backgroundColor","red")
    })

    $("#btn2").click(function () {
        $("body>div").css("backgroundColor","red")
    })
</script>
</html>

```

### 

### 4.3 选择器：属性选择器

| **属性选择器**                            | **语法**          |
| ----------------------------------------- | ----------------- |
| **获得有属性名的元素**                    | 标签名[属性名]    |
| **获得属性名** **等于**   **值** **元素** | 标签名[属性名=值] |



```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
 <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
 <title>03-属性选择器.html</title>
 <!--   引入jQuery -->
 <script src="../js/jquery-3.3.1.js" type="text/javascript"></script>
 <link rel="stylesheet" type="text/css" href="./css/style.css" />  
 <script type="text/javascript">
  </script>
</head>
<body>
  <button id="reset">手动重置页面元素</button>
  <input type="checkbox" id="isreset" checked="checked"/><label for="isreset">点击下列按钮时先自动重置页面</label>	



    <h3> 属性选择器.</h3>
	<h3>常见选择器</h3>
  <input type="button" value="选取含有 属性title 的div元素." id="btn1"/>
  <input type="button" value="选取 属性title值等于“test”的div元素." id="btn2"/>


	<br /><br />
   <div class="one" id="one" >
 id为one,class为one的div
      <div class="mini">class为mini</div>
  </div>

    <div class="one"  id="two" title="test" >
	 id为two,class为one,title为test的div.
      <div class="mini"  title="other">class为mini,title为other</div>
      <div class="mini"  title="test">class为mini,title为test</div>
  </div>

  <div class="one">
      <div class="mini">class为mini</div>
      <div class="mini">class为mini</div>
	  <div class="mini">class为mini</div>
	  <div class="mini"></div>
  </div>

  

  <div class="one">
      <div class="mini">class为mini</div>
      <div class="mini">class为mini</div>
	  <div class="mini">class为mini</div>
	  <div class="mini"  title="tesst">class为mini,title为tesst</div>
  </div>


  <div style="display:none;"  class="none">style的display为"none"的div</div>
  
  <div class="hide">class为"hide"的div</div>
 
  <div>
  包含input的type为"hidden"的div<input type="hidden" size="8"/>
  </div>

    <script src="../js/jquery-3.3.1.js"></script>

    <script !src="">

        // 属性选择器
        $("#btn1").click(function () {
            $("div[title]").css("backgroundColor", "red");
        });

        $("#btn2").click(function () {
            $("div[title='test']").css("backgroundColor", "red");
        });


    </script>

</body>
</html>
```

### 

### 4.4 选择器：基本过滤选择器

**什么是过滤选择器**

通过其它的选择器选中一组元素以后，在这个基础上再次进行过滤得到的元素。

| **基本过滤选择器**                 | **语法** |
| ---------------------------------- | -------- |
| **获得选择的元素中的第一个元素**   | :first   |
| **获得选择的元素中的最后一个元素** | :last    |
| **不包括指定内容的元素**           | :not()   |
| **偶数，从 0** **开始计数**        | :even    |
| **奇数，从 0** **开始计数**        | :odd     |
| **等于第几个**                     | :eq()    |
| **大于第n个，不含第index个**       | :gt()    |
| **小于第n个，不含第index个**       | :lt()    |



```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
 <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
 <title>04-基本过滤选择器.html</title>
 <!--   引入jQuery --> 
 <script src="../js/jquery-3.3.1.js" type="text/javascript"></script>
 <link rel="stylesheet" type="text/css" href="./css/style.css" />   
  <script type="text/javascript">
  </script>
</head>
<body>
  <h3>基本过滤选择器.</h3>
  <button id="reset">手动重置页面元素</button>
  <input type="checkbox" id="isreset" checked="checked"/><label for="isreset">点击下列按钮时先自动重置页面</label><br /><br />

  <input type="button" value="选择第一个div元素." id="btn1"/>
  <input type="button" value="选择最后一个div元素." id="btn2"/>
  <input type="button" value="选择class不为one的 所有div元素." id="btn3"/>
  <input type="button" value="选择索引值为偶数 的div元素." id="btn4"/>
  <input type="button" value="选择索引值为奇数 的div元素." id="btn5"/>
  <input type="button" value="选择索引值等于3的元素." id="btn6"/>
  <input type="button" value="选择索引值大于3的元素." id="btn7"/>
  <input type="button" value="选择索引值小于3的元素." id="btn8"/>

 
<br /><br />
 
   <!-- 测试的元素 -->
  <div class="one" id="one" >
 id为one,class为one的div
      <div class="mini">class为mini</div>
  </div>

  <div class="one"  id="two" title="test" >
	 id为two,class为one,title为test的div.
      <div class="mini"  title="other">class为mini,title为other</div>
      <div class="mini"  title="test">class为mini,title为test</div>
  </div>

  <div class="one">
      <div class="mini">class为mini</div>
      <div class="mini">class为mini</div>
	  <div class="mini">class为mini</div>
	  <div class="mini"></div>
  </div>

  

  <div class="one">
      <div class="mini">class为mini</div>
      <div class="mini">class为mini</div>
	  <div class="mini">class为mini</div>
	  <div class="mini"  title="tesst">class为mini,title为tesst</div>
  </div>


  <div style="display:none;"  class="none">style的display为"none"的div</div>
  
  <div class="hide">class为"hide"的div</div>
 
  <div>
  包含input的type为"hidden"的div<input type="hidden" size="8"/>
  </div>

  
  <span id="mover">正在执行动画的span元素.</span>

    <script src="../js/jquery-3.3.1.js"></script>

    <script !src="">

        // 1.基础过滤器：选择第一个元素--：frist
        // 语法：$(":过滤器")
        $("#btn1").click(function () {
            $("div:first").css("backgroundColor", "red");
        });

        // 2.基础过滤器：选择最后一个元素--：last
        $("#btn2").click(function () {
            $("div:last").css("backgroundColor", "red");
        });


        // 3.基础过滤器：排除过滤--：not(xxx)
        $("#btn3").click(function () {
            $("div:not('.one')").css("backgroundColor", "red");
        });


        // 4.基础过滤器：选择偶数--：even
        $("#btn4").click(function () {
            $("div:even").css("backgroundColor", "red");
        });

        // 5.基础过滤器：选择奇数--：odd
        $("#btn5").click(function () {
            $("div:odd").css("backgroundColor", "red");
        });


        // 6.基础过滤器：选择指定元素--：eq
        $("#btn6").click(function () {
            $("div:eq(3)").css("backgroundColor", "red");
        });

        // 7.基础过滤器：大于过滤器--：gt
        $("#btn7").click(function () {
            $("div:gt(3)").css("backgroundColor", "red");
        });

        // 8.基础过滤器：小于过滤器--：lt
        $("#btn8").click(function () {
            $("div:lt(3)").css("backgroundColor", "red");
        });


    </script>

</body>
</html>
```

### 

## 六.  Jquery的DOM操作

### 6.1 Jquery操作内容

```markdown
1. text(): 获取/设置元素的标签体纯文本内容
		* 相当于js： innerText属性

2. html(): 获取/设置元素的标签体超文本内容
		* 相当于js： innerHTML属性
```

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
	<meta charset="UTF-8">
	<title>01-dom操作内容</title>
	<script src="../js/jquery-3.3.1.js" type="text/javascript"></script>
</head>
<body>
<div id="myDiv">
	<span>天王盖地虎</span> <br>
	<span>小鸡炖蘑菇</span>
</div>
	<script src="../js/jquery-3.3.1.js"></script>

<script>
	//html()--内容中可以包含text和标签，标签会被浏览器解析
	// 1.获得元素中的所有内容（包括标签）
	// var div_html_jq = $("#myDiv").html();
	// console.log(div_html_jq);
	// 2.修改元素中的内容（包括标签）
	// $("#myDiv").html("<h1>独臂玩家</h1>");

	// 3.追加元素中的内容（包括标签）
	// $("#myDiv").html($("#myDiv").html()+"<h1>独臂玩家</h1>");
	$("#myDiv").append("<h1>独臂玩家</h1>");

	// text--内容中只包含text，
	// 修改：标签会当成字符内容显示
	// 获得：只会获得元素中的所哟text,不包括标签
	// 1.获得元素中的text
	// var div_text_jq = $("div>span:eq(0)").text();
	// console.log(div_text_jq);
	// var div_text_jq = $("#myDiv").text();
	// console.log(div_text_jq);

</script>
</body>
</html>
```





### 6.2  Jquery操作属性

```markdown
1. val()： 获取/设置元素的value属性值
		* 相当于：js对象.value
		
2. attr(): 获取/设置元素的属性
        	文本属性(String)
3. prop():获取/设置元素的属性
			状态属性(boolean)
```



```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
	<meta charset="UTF-8">
	<title>02-dom操作属性</title>
	<script src="../js/jquery-3.3.1.js" type="text/javascript"></script>
</head>
<body>
<form action="#" method="get">
	姓名 <input type="text" name="username" id="username" value="德玛西亚"/> <br/>

	爱好
	<input id="hobby" type="checkbox" name="hobby" value="perm" checked>烫头<br/>

	<input type="reset" value="重置按钮"/>
	<input type="submit" value="提交按钮"/><br/>
</form>

<!--<script>
		//1. 获取value属性
	var username = document.getElementById("username");
    console.log(username.value); //获取
    username.value = "呵呵" // 重置
		//2. 获取其他文本属性
	var hobby = document.getElementById("hobby");
    console.log(hobby.type);
    // hobby.type = "radio"
		//3. 获取选中状态(checkbox/radio : checked , option : selected)
			// boolean : true表示选中, false表示没有选中
        console.log(hobby.checked);
        hobby.checked = false
</script>-->
<script >
/*
*  1. val()： 获取/设置元素的value属性值
			* 相当于：js对象.value

	2. attr(): 获取/设置元素的属性 (attribute)
			文本
	3. prop():获取/设置元素的属性 (properties)
			boolean
* */
	//1. value属性
	var val = $("#username").val();
   console.log(val);

	$("#username").val("呵呵")

	//2. 其他文本属性
	var attr = $("#hobby").attr("type");
	console.log(attr);

	// $("#hobby").attr("type","radio");

	//3. 选中状态
	var prop = $("#hobby").prop("checked");
	console.log(prop);

	$("#hobby").prop("checked",false);
</script>

</body>
</html>
```





### 6.3  Jquery操作样式

```markdown
# 语法
		jq对象.css()
			css(样式名) 获取
			css(样式名,样式值) 设置
		优点：支持css语法
		举例：font-size
			
```

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
	<meta charset="UTF-8">
	<title>03-dom操作样式</title>
	<script src="../js/jquery-3.3.1.js" type="text/javascript"></script>
	<style>
		#p1{ background-color: #ff0000;}
		.p2{ background-color: #ff0000;}
	</style>

</head>
<body>
<p id="p1">第一个p标签</p>
<p >第二个p标签</p>

	<script src="../js/jquery-3.3.1.js"></script>

	<script>

		// 1.获得样式
		var css = $("#p1").css("backgroundColor");
		console.log(css);

		// 2.设置样式
		$("#p1").css("backgroundColor","blue");

		// 3.通过class来设置样式--addClass(推荐)
		$("p:eq(1)").addClass("p2");

	</script>

</body>
</html>
```





### 6.4  Jquery操作元素

```markdown
1. $.append()   内部追加

2. $.empty()  清空所有子元素 
```



```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
	<meta charset="UTF-8">
	<title>04-dom操作元素</title>
	<script src="../js/jquery-3.3.1.js" type="text/javascript"></script>
</head>
<body>
<ol id="star">
	<li>古力娜扎</li>
	<li>迪丽热巴</li>
</ol>

<ul id="music">
	<li >月亮代表我的心</li>
	<li >千千阙歌</li>
</ul>

<!--<script>
	//1. 追加
	var star = document.getElementById("star");
	star.innerHTML += '<li>马尔扎哈</li>'

	//2. 清空内部
    var music = document.getElementById("music");
	music.innerHTML = ""
</script>-->

<script >
	/*
	*  1. $.append()   内部追加

		2. $.empty()  清空所有子元素
	* */
	$("#star").append("<li>马尔扎哈2</li>")

    $("#music").empty()
</script>
</body>
</html>
```



## 七 综合案例

### 7.1 隔行换色


 <figure class="thumbnails">
    <img src="picture/Ajax&json/1586499039991.png" alt="Screenshot of coverpage" title="Cover page">
</figure>

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8"/>
    <title>01-案例-隔行变色</title>
    <script src="../js/jquery-3.3.1.min.js"></script>
</head>
<body>
<table id="tab1" border="1" width="800" align="center">
    <tr>
        <th width="100px"><input type="checkbox">全/<input type="checkbox">反选</th>
        <th>分类ID</th>
        <th>分类名称</th>
        <th>分类描述</th>
        <th>操作</th>
    </tr>
    <tr>
        <td><input type="checkbox" class="checkbox"></td>
        <td>1</td>
        <td>手机数码</td>
        <td>手机数码类商品</td>
        <td><a href="">修改</a>|<a href="">删除</a></td>
    </tr>
    <tr>
        <td><input type="checkbox" class="checkbox"></td>
        <td>2</td>
        <td>电脑办公</td>
        <td>电脑办公类商品</td>
        <td><a href="">修改</a>|<a href="">删除</a></td>
    </tr>
    <tr>
        <td><input type="checkbox" class="checkbox"></td>
        <td>3</td>
        <td>鞋靴箱包</td>
        <td>鞋靴箱包类商品</td>
        <td><a href="">修改</a>|<a href="">删除</a></td>
    </tr>
    <tr>
        <td><input type="checkbox" class="checkbox"></td>
        <td>4</td>
        <td>家居饰品</td>
        <td>家居饰品类商品</td>
        <td><a href="">修改</a>|<a href="">删除</a></td>
    </tr>
    <tr>
        <td><input type="checkbox" class="checkbox"></td>
        <td>5</td>
        <td>牛奶制品</td>
        <td>牛奶制品类商品</td>
        <td><a href="">修改</a>|<a href="">删除</a></td>
    </tr>
    <tr>
        <td><input type="checkbox" class="checkbox"></td>
        <td>6</td>
        <td>大豆制品</td>
        <td>大豆制品类商品</td>
        <td><a href="">修改</a>|<a href="">删除</a></td>
    </tr>
    <tr>
        <td><input type="checkbox" class="checkbox"></td>
        <td>7</td>
        <td>海参制品</td>
        <td>海参制品类商品</td>
        <td><a href="">修改</a>|<a href="">删除</a></td>
    </tr>
    <tr>
        <td><input style="" type="checkbox" class="checkbox"></td>
        <td>8</td>
        <td>羊绒制品</td>
        <td>羊绒制品类商品</td>
        <td><a href="">修改</a>|<a href="">删除</a></td>
    </tr>
    <tr>
        <td><input  type="checkbox" class="checkbox"></td>
        <td>9</td>
        <td>海洋产品</td>
        <td>海洋产品类商品</td>
        <td><a href="">修改</a>|<a href="">删除</a></td>
    </tr>
    <tr>
        <td><input type="checkbox" class="checkbox"></td>
        <td>10</td>
        <td>奢侈用品</td>
        <td>奢侈用品类商品</td>
        <td><a href="">修改</a>|<a href="">删除</a></td>
    </tr>
</table>

    <script src="../js/jquery-3.3.1.js"></script>

    <!--<script !src="">

        // js实现各行换色
        let trs_js = document.querySelectorAll("tr");

        for (let i = 1; i < trs_js.length; i++) {

            if (i % 2 == 0 ) {
                trs_js[i].style.backgroundColor = "green";
            }

            let oldBgc = '';
            trs_js[i].onmouseover=function () {
                // 内置对象：this&#45;&#45;当前tr对象
                // oldBgc = this.style.backgroundColor;
                oldBgc =  trs_js[i].style.backgroundColor;
                this.style.backgroundColor = "yellow";
            }


            //
            trs_js[i].onmouseout=function () {
                this.style.backgroundColor=oldBgc;
            }
        }



    </script>-->

    <script !src="">

        $("tr:gt(0):odd").css("backgroundColor", "red");

        let oldBgc = '';
        $("tr:gt(0)").mouseover(function () {
            // this---代表集合循环遍历时的当前对象(JS对象)
            // js方法实现
            // oldBgc = this.style.backgroundColor;
            // this.style.backgroundColor = "yellow";
            // jquery方式实现
            oldBgc = $(this).css("backgroundColor");
            $(this).css("backgroundColor", "yellow");
        })

        $("tr:gt(0)").mouseout(function () {
            // this---代表集合循环遍历时的当前对象(JS对象)
            this.style.backgroundColor = oldBgc;
        })
    </script>


</body>
</html>
```



### 7.2 商品全选

```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>案例-商品全选</title>
    <script src="../js/jquery-3.3.1.min.js"></script>
</head>
<body>
<!--
商品全选
    1. 全选 点击全选按钮,所有复选框都被选中
    2. 反选 点击反选按钮,所有复选框状态取反
-->
<button id="btn1">1. 全选</button>
<button id="btn2">2. 反选</button>
<br/>
<input type="checkbox">电脑
<input type="checkbox">手机
<input type="checkbox">汽车
<input type="checkbox">别墅
<input type="checkbox" checked="checked">笔记本

    <script src="../js/jquery-3.3.1.js"></script>

    <!--<script !src="">

        let btn1_js = document.querySelector("#btn1");
        let btn2_js = document.querySelector("#btn2");
        btn1_js.onclick=function () {
            let inputs_js = document.querySelectorAll("input[type='checkbox']");

            for (let i = 0; i < inputs_js.length; i++) {
                inputs_js[i].checked = true;
            }
        }

        btn2_js.onclick=function () {
            let inputs_js = document.querySelectorAll("input[type='checkbox']");

            for (let i = 0; i < inputs_js.length; i++) {
                inputs_js[i].checked = !inputs_js[i].checked ;
            }
        }

    </script>-->

    <script !src="">

        $("#btn1").click(function () {
            $("input[type='checkbox']").prop("checked", true);
        })

        $("#btn2").click(function () {
            //jquery中的方法click()--可以将checkbox中的true和false进行切换
            // 方式一：
            // $("input[type='checkbox']").click();

            // 方式二：
            let inputs_jq = $("input[type='checkbox']");

            //$.each 方法可以循环遍历jq对象的内容
            // 参数:
            //  1.jq集合
            //  2.function回调函数--循环每一个元素时都会调用当前方法
            $.each(inputs_jq,function (i, e) {
                //i:集合的下标
                //e:循环当前元素的对象（js对象）
                e.checked = !e.checked;
            })
        })



    </script>


<script >

</script>
</body>
</html>
```




