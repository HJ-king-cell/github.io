---
typora-copy-images-to: img
typora-root-url: ./
---

# 1、Vue 快速入门

## 1.1、Vue的介绍

- Vue是一套构建用户界面的渐进式前端框架。

  > “渐进式框架”简单的来说, 就是用你想用或者能用的功能特性，你不想用的部分功能可以先不用。Vue不强求你一次性接受并使用它的全部功能特性。

- 只关注视图层，并且非常容易学习，还可以很方便的与其它库或已有项目整合。

  > MVC: 
  >
  > ​	model,  模型层
  >
  > ​	view,  视图层
  >
  > ​	controller 控制层

- 通过尽可能简单的API来实现响应数据的绑定和组合的视图组件。

- 特点
  易用：在有HTML,CSS, JavaScript的基础上，快速上手。
  灵活：简单小巧的核心，渐进式技术栈，足以应付任何规模的应用。
  性能：20kbmin+gzip运行大小、超快虚拟DOM、最省心的优化。

## 1.2、Vue的快速入门

- **开发步骤**

1. 下载和引入vue.js文件。
2. 编写入门程序。
   视图：负责页面渲染，主要由HTML+CSS构成。
   脚本：负责业务数据模型（Model）以及数据的处理逻辑。

- **代码实现**

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>快速入门</title>
  </head>
  <body>
      <div id="app">
          <!--3.在html中写入Vue的插值表达式:{{  }}-->
          <!--Vue会将插值表达是最终会替换为具体的内容-->
          {{message}}
      </div>
      <!--除了Vue接管的范围，就不会在进行数据的管理-->
           {{message}}
  </body>
      <!-- 第一步：将vue.js引入页面中 -->
  <script src="js/vue.js"></script>
  
      <!-- 第二步：编写js代码（Vue的代码） -->
      <script>
  
          // 1.创建Vue对象
          // 2.给Vue对象进行属性的赋值
          /*
              属性（基础）：
                  el：告诉Vue接管的页面区域
                  data:定义Vue对象的数据
           */
          let app = new Vue({
              el:"#app",
              data:{
                  message:"传智播客"
              }
          });
  
      </script>
  </html>
  ```

## 1.3、Vue快速入门详解

- Vue 核心对象：每一个 Vue 程序都是从一个 Vue 核心对象开始的。

  ```tex
  let vm = new Vue({
   选项列表;
  });
  ```

- 选项列表

  ```tex
  1. el选项：指定的vue控制区域。(根据选择器获取)
  2. data选项：用于保存当前Vue对象中的数据。在视图中声明的变量需要在此处赋值。
  ```

- 数据绑定

  ```js
  在视图部分获取脚本部分的数据: (插值表达式)
  	{{变量名}}
  ```

## 1.4、Vue快速入门的升级

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>快速入门升级</title>
</head>
<body>
    <!-- 视图 -->
    <div id="div">
        <div>姓名：{{name}}</div>
        <div>班级：{{classRoom}}</div>
        <button onclick="hi()">打招呼</button>
        <button onclick="update()">修改班级</button>
    </div>
</body>
<script src="js/vue.js"></script>
<script>
  let app = new Vue({
      el:"#div",
      data:{
          name:"金豆",
          classRoom:"javaEE 113"
      }
  });


  /* 通过Vue对象实例的变量名可以直接或的Vue对象中的数据
  *     Vue对象.属性名
  *  */
  function hi() {
      console.log(app.name+"在教室里"+app.classRoom+"打招呼")
  }

  function update() {
      app.classRoom = "超级 JavaEE 113 ";
  }
  

</script>
</html>
```

## 1.5、Vue小结

- Vue是一套构建用户界面的渐进式前端框架。
- Vue的程序包含视图和脚本两个核心部分。
- 脚本部分
  - Vue核心对象。
  - 选项列表
    - el：接收获取的元素。
    - data：保存数据。
    - methods：定义方法。
- 视图部分
  - 数据绑定：{{变量名}}

# 2、Vue 常用指令

## 2.1、指令介绍

- 指令：是带有 v- 前缀的特殊属性，不同指令具有不同含义。例如 v-html，v-if，v-for。

- 使用指令时，通常编写在标签的属性上，值可以使用 JS 的表达式。

- 常用指令

  ![](.\img\常用指令-指令介绍.png)

## 2.2、文本插值

- v-html：把文本解析为 HTML 代码。

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>文本插值</title>
  </head>
  <body>
  <!--
      v-html：把文本解析为 HTML 代码。
          备注:
              插值表达式,v-text指令 都不能解析html标签(纯文本)
  -->
      <div id="div">
          <div>{{msg}}</div>
  
          <div v-html="msg"></div>
          <div v-text="msg"></div>
      </div>
  </body>
  <script src="js/vue.js"></script>
  <script>
      new Vue({
          el:"#div",
          data:{
              msg:"<h1>Hello Vue</h1>"
          }
      });
  </script>
  </html>
  ```

## 2.3、绑定属性

- v-bind：为 HTML 标签绑定属性值。

  ```html
  <!DOCTYPE html>
  <html lang="en" xmlns:v-bind="http://www.w3.org/1999/xhtml" xmlns:v-on="http://www.w3.org/1999/xhtml">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>绑定属性</title>
      <style>
          .my{
              border: 1px solid red;
          }
      </style>
  </head>
  
  <body>
      <div id="div">
          <!--
              插件表达式不能写在属性中!!!
          -->
          <a href="{{url}}">百度一下 </a>
  
          <br>
          <!--
              v-bind：为 HTML 标签绑定属性值
                  v-bind:属性名="data变量"
          -->
          <a v-bind:href="url">百度一下</a>
          <br>
          <!--
              v-bind 可以省略不写
          -->
          <a :href="url">百度一下</a>
          <br>
          <!--
              也可以绑定其他属性
          -->
          <div class="my">我是div</div>
          <div :class="cls">我是div</div>
      </div>
  </body>
  <script src="js/vue.js"></script>
  <script>
      new Vue({
          el:"#div",
          data:{
              url:"http://www.baidu.com",
              cls:"my"
          }
      });
  </script>
  </html>
  ```

## 2.4、条件渲染

- v-if：条件性的渲染某元素，判定为真时渲染,否则不渲染。

- v-else：条件性的渲染。

- v-else-if：条件性的渲染。

- v-show：根据条件展示某元素，区别在于切换的是display属性的值。

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>条件渲染</title>
  </head>
  <body>
      <div id="div">
          <!-- 判断num的值，对3取余
              余数为0显示div1
              余数为1显示div2
              否则显示div3 -->
          <div v-if="num % 3 == 0">div1</div>
          <div v-else-if="num % 3 == 1">div2</div>
          <div v-else>div3</div>
  
  
          <div v-show="flag">div4</div>
  
          <!--
             v-if  v-show 他们俩虽然都是控制元素是否显示，但是底层的原理不一样
                 v-if 如果条件为false，页面中根本没有这个元素
                 v-show如果条件为false，页面中有这个元素只不过它的display属性值为none
         -->
      </div>
  </body>
  <script src="js/vue.js"></script>
  <script>
      new Vue({
          el:"#div",
          data:{
              num:1,
             flag:false
          }
      });
  </script>
  </html>
  ```

## 2.5、列表渲染

- v-for：列表渲染，遍历容器的元素或者对象的属性。

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>列表渲染</title>
  </head>
  <body>
      <div id="div">
          <ul>
              <!--
                v-for：列表渲染，遍历容器的元素或者对象的属性。
                     -->
              <!--
                  1. 类似于增强for循环
                      a. 遍历数组
  
                          names:["张三","李四","王五"]
                      element : 每次遍历出来的元素
                      names : 被遍历的数组
              -->
              <li v-for="element in names">
                  {{element}}
              </li>
              <!--
                    b. 遍历对象
                        student:{
                            name:"张三",
                            age:23
                        }
                -->
              <li v-for="element in student">
                {{element}}
              </li>
              <!--
                  2. 类似于普通for循环
                      (元素,索引) in 被遍历的数组
              -->
              <li v-for="(element,index) in names">
                  元素:{{element}},索引:{{index}}
              </li>
  
              <li v-for="(element,index) in student">
                  元素:{{element}},索引:{{index}}
              </li>
          </ul>
      </div>
  </body>
  <script src="js/vue.js"></script>
  <script>
      new Vue({
          el:"#div",
          data:{
              names:["张三","李四","王五"],
              student:{
                  name:"张三",
                  age:23
              }
          }
      });
  </script>
  </html>
  ```

## 2.6、事件绑定

- v-on：为 HTML 标签绑定事件。

  ```html
  <!DOCTYPE html>
  <html lang="en" xmlns:v-on="http://www.w3.org/1999/xhtml">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>事件绑定</title>
  </head>
  
  <body>
      <div id="div">
          <div>{{name}}</div>
  
          <!--
              v-on：为 HTML 标签绑定事件
  
               1. 完整
                      v-on:事件名="函数调用"
  
                  2. 简略
                      @:事件名="函数调用"
                      
          -->
          <!--  js中的onclick 会寻找js的方法-->
  <!--        <button onclick="v_change()">单击_改变div的内容</button>-->
          <button v-on:click="v_change()">单击_改变div的内容</button>
          <button v-on:dblclick="v_dblchange()">双击_改变div的内容</button>
  
          <button @click="v_shorteningChange()">简写_改变div的内容</button>
      </div>
  </body>
  <script src="js/vue.js"></script>
  <script>
      /* 为了让Vue管理事件的回调函数，回调函数要在Vue对象中进行定义
      *   方法定义的位置：methods定义  （methods：{}）
      *   在Vue对象中存在一个对象：this-->当前的Vue对象
      *  */
      new Vue({
          el:"#div",
          data:{
              name:"黑马程序员"
          },
          methods:{
              //命名方式
              // v_change(){
              //     this.name = "传智播客";
              // }
              // 匿名方式
              v_change:function () {
                  this.name = "传智播客";
              },
              v_dblchange:function () {
                  this.name = "小浩浩";
              },
              v_shorteningChange:function () {
                  this.name = "马金斗";
              }
          }
      });
  
      // js方法定义
      // 命名、匿名
      // function js_fun() {
      //
      // }
      //
      // var js_fun = function () {
      //
      //
      // };
  
  </script>
  </html>
  ```

## 2.7、表单绑定

- **表单绑定**
  v-model：在表单元素上创建双向数据绑定。

- **双向数据绑定**

  单行：

  ​	更新Vue模型数据(data属性)，视图（页面）中的数据也会更新。(单向)

  双向：

  ​	更新Vue模型数据(data属性)，视图（页面）中的数据也会更新。

  ​	更新视图（页面）数据，Vue模型数据(data属性) 也会更新。

- **MVVM模型(Model,View,ViewModel)：是MVC模式的改进版**
  在前端页面中，JS对象表示Model，页面表示View，两者做到了最大限度的分离。
  将Model和View关联起来的就是ViewModel，它是桥梁。
  ViewModel负责把Model的数据同步到View显示出来，还负责把View修改的数据同步回Model。

  ![](.\img\1539348598180.png)

  

  ```html
  <!DOCTYPE html>
  <html lang="en" xmlns:v-bind="http://www.w3.org/1999/xhtml">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>表单绑定</title>
  </head>
  <body>
      <div id="div">
          <form autocomplete="off">
              <!--
                  单向绑定
              -->
              姓名_单向绑定：<input type="text" name="username" :value="username" >
              <br>
              <!--
                  双向绑定
                      v-model会将模型数据绑定到input的value属性
              -->
              姓名_双向绑定：<input type="text" name="username" v-model="username">
              <br>
              年龄：<input type="number" name="age" v-model="age">
              性别:<input type="text" name="gender" v-model="gender">
  
          </form>
  
          <hr>
      </div>
  </body>
  <script src="js/vue.js"></script>
  <script>
      new Vue({
          el:"#div",
          data:{
              username:"张三",
              age:23,
              gender:"男"
          }
      });
  </script>
  </html>
  ```

## 2.8、小结

- **指令：是带有v-前缀的特殊属性，不同指令具有不同含义。**

- **文本插值**
  v-html 和v-text

  功能是给视图（页面）添加数据的，v-html中有标签会将标签进行解析，v-text内容会当成纯文本内容显示。

- **绑定属性**
  v-bind 通过 Vue 来绑定 html中标签属性

  > 简写：:属性名=‘‘

- **条件渲染**

		v-if  	v-else-if  v-else 进行条件判断并控制视图内容的显示

		v-show  进行条件判断并控制视图内容的显示
	
		区别：
	
		v-if  	v-else-if  v-else 将不符合条件的内容从视图上消失
	
		v-show 将不符合条件的内容通过css样式来隐藏(display)

- **列表渲染**

  v-for 进行数据模型在视图中的遍历显示数据

  方式一：

  ​	v-for=“ 变量名 in 集合或对象  ”

  方式二：

  ​	v-for=“ (变量名,小标或属性名的变量) in 集合或对象  ”

- **事件绑定**

  v-on:绑定视图中的事件，事件触发的回调函数必须要在Vue对象中声明。

  >简写：@事件名

- **表单绑定**
  单项数据绑定：v-bind:value

  双项数据绑定：v-model（只对input的value生效）



# 3、Vue的生命周期

## **3.1 生命周期**

![](img/Vue%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png) 



## **3.2 生命周期的八个阶段**

![](img/%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E7%9A%84%E5%85%AB%E4%B8%AA%E9%98%B6%E6%AE%B5.png) 



## **3.3 代码(了解)**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>生命周期</title>
    <script src="vue/vue.js"></script>
</head>
<body>
    <div id="app">
        {{message}}
    </div>
</body>
<script>
    let vm = new Vue({
				el: '#app',
				data: {
					message: 'Vue的生命周期'
				},
				beforeCreate: function() {
					console.group('------beforeCreate创建前状态------');
					console.log("%c%s", "color:red", "el     : " + this.$el); //undefined
					console.log("%c%s", "color:red", "data   : " + this.$data); //undefined 
					console.log("%c%s", "color:red", "message: " + this.message);//undefined
				},
				created: function() {
					console.group('------created创建完毕状态------');
					console.log("%c%s", "color:red", "el     : " + this.$el); //undefined
					console.log("%c%s", "color:red", "data   : " + this.$data); //已被初始化 
					console.log("%c%s", "color:red", "message: " + this.message); //已被初始化
				},
				beforeMount: function() {
					console.group('------beforeMount挂载前状态------');
					console.log("%c%s", "color:red", "el     : " + (this.$el)); //已被初始化
					console.log(this.$el);
					console.log("%c%s", "color:red", "data   : " + this.$data); //已被初始化  
					console.log("%c%s", "color:red", "message: " + this.message); //已被初始化  
				},
				mounted: function() {
					console.group('------mounted 挂载结束状态------');
					console.log("%c%s", "color:red", "el     : " + this.$el); //已被初始化
					console.log(this.$el);
					console.log("%c%s", "color:red", "data   : " + this.$data); //已被初始化
					console.log("%c%s", "color:red", "message: " + this.message); //已被初始化 
				},
				beforeUpdate: function() {
					console.group('beforeUpdate 更新前状态===============》');
					let dom = document.getElementById("app").innerHTML;
					console.log(dom);
					console.log("%c%s", "color:red", "el     : " + this.$el);
					console.log(this.$el);
					console.log("%c%s", "color:red", "data   : " + this.$data);
					console.log("%c%s", "color:red", "message: " + this.message);
				},
				updated: function() {
					console.group('updated 更新完成状态===============》');
					let dom = document.getElementById("app").innerHTML;
					console.log(dom);
					console.log("%c%s", "color:red", "el     : " + this.$el);
					console.log(this.$el);
					console.log("%c%s", "color:red", "data   : " + this.$data);
					console.log("%c%s", "color:red", "message: " + this.message);
				},
				beforeDestroy: function() {
					console.group('beforeDestroy 销毁前状态===============》');
					console.log("%c%s", "color:red", "el     : " + this.$el);
					console.log(this.$el);
					console.log("%c%s", "color:red", "data   : " + this.$data);
					console.log("%c%s", "color:red", "message: " + this.message);
				},
				destroyed: function() {
					console.group('destroyed 销毁完成状态===============》');
					console.log("%c%s", "color:red", "el     : " + this.$el);
					console.log(this.$el);
					console.log("%c%s", "color:red", "data   : " + this.$data);
					console.log("%c%s", "color:red", "message: " + this.message);
				}
			});

		
			// 销毁Vue对象
			//vm.$destroy();
			//vm.message = "hehe";	// 销毁后 Vue 实例会解绑所有内容

			// 设置data中message数据值
			vm.message = "good...";
</script>
</html>
```



**生命周期的特点：**

```markdown
#创建
  1. 创建前：
  	el: 没有接管区域内容
  	data：Vue的data属性无初始化
  	message：data属性的message值没有初始化
  2. 创建后：
  	el: 没有接管区域内容
  	data：Vue的data属性初始化   ----（变化）
  	message：data属性的message值初始化 ----（变化）

#挂载
  1. 挂载前：
  	el: 接管区域内容，插值表达式无赋值 ----（变化）
  	data：无变化
  	message：无变化
  2. 挂载后：
  	el: 接管区域内容，插值表达式赋值 ----（变化）
  	data：无变化
  	message：无变化
#更改
  1. 更改前：
  	el: 无变化
  	data：无变化
  	message：data属性的message值跟新为最新内容
  2. 更改后：
  	el: 插值表达式更新为最新值 ----（变化）
  	data：无变化
  	message：data属性的message值跟新为最新内容----（变化）
#销毁
  1. 销毁前：
  	el: 无变化
  	data：无变化
  	message：无变化
  2. 销毁后：
  	el: 内容无变化，但不在更改里面内容 ----（变化）
  	data：无变化
  	message：无变化
```





## 3.4 运用举例

```markdown
# 了解生命周期，掌握常用的 created方法！    
1. 此方法是 data初始化完成, 页面标签数据初始化之前执行的方法
2. 通常在此方法中，我们会发起后台数据请求，在渲染页面标签数据之前，先获取后台数据，进行data 数据的模型赋值！
```



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>生命周期</title>
    <script src="js/vue.js"></script>
</head>
<body>
<div id="app">
    {{message}}
    <div v-for="user in list">
        {{user.name}}
    </div>

    <hr>
</div>
</body>
<script>

    new Vue({
        el:"#app",
        data:{
            message:"好友列表",
            list:[] //用户信息的集合
        },
        methods:{
            queryUsers(){
                //向后台发起查询
                console.log("对list进行赋值")
            }
        },
        //  data初始化完成, 页面标签数据初始化之前执行的方法
        created(){
            //一般我们在这个方法中去对数据进行初始化，比如调用queryUsers方法从后台查询初始数据对list变量赋值
            this.queryUsers();
        }
    });
</script>
</html>
```



# 4. Vue异步操作

## 4.1 axios介绍

- **在Vue中发送异步请求，本质上还是AJAX。我们可以使用axios这个插件来简化操作！**

- **使用步骤**
  1.引入axios核心js文件。
  2.调用axios对象的方法来发起异步请求。
  3.调用axios对象的方法来处理响应的数据。

- **axios常用方法**

  ![](img/axios常用方法.png) 

- **代码实现**

  - **html代码**

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>异步操作</title>
      <script src="js/vue.js"></script>
      <!--
          引入axios核心js文件
      -->
      <script src="js/axios-0.18.0.js"></script>
  </head>
  <body>
  <div id="div">
      {{name}}
      <button @click="send()">发起请求</button>
  </div>
  </body>
  <script>
      new Vue({
          el:"#div",
          data:{
              name:"张三",
              age:18
          },
          methods:{
              send:function () {
  
                  // 1.异步请求--axios
                  // 方法说明：
                  /*
                      get：ajax的get请求
                      then：请求成功并响应成功会调用此回调函数
                      catch：请求失败或响应失败会调用此回调函数
                   */
                  //get参数：url?name=value
                  //then的回调函数要使用ES6后的箭头方法
                  //then方法里会向回调函数中传入响应的结果
                  //catch方法会在失败是会调用，并将错误传入到回调函数中
                  // axios.get(`../AjaxServlet?name=${this.name}&age=${this.age}`)
                  // .then( resp => {
                  //     /*
                  //         axios响应成功后，会将所有的响应内容放到resp
                  //             属性：data-->服务器响应的内容
                  //      */
                  //     this.name = resp.data.name;
                  // }).catch(error =>{
                  //     console.log(error);
                  // })
  
                  //post参数：
                  //     url :请求路径
                  //     param：请求参数
                  axios.post(`../AjaxServlet`,`name=${this.name}`)
                      .then( resp => {
                          /*
                              axios响应成功后，会将所有的响应内容放到resp
                                  属性：data-->服务器响应的内容
                           */
                          this.name = resp.data.name;
                      }).catch(error =>{
                      console.log(error);
                  })
  
              }
          },
          created(){
              /* 页面加载后，创建Vue对象，此时就会请求服务端获得数据*/
              this.send();
          }
  
      });
  
  
  </script>
  </html>
  ```

  - **java代码**

  ```java
  package com.itheima.web.servlet;
  
  import com.alibaba.fastjson.JSON;
  import com.itheima.domain.User;
  
  import java.io.IOException;
  
  /**
   * <p></p>
   *
   * @Description:
   */
  @javax.servlet.annotation.WebServlet("/AjaxServlet")
  public class AjaxServlet extends javax.servlet.http.HttpServlet {
      protected void doPost(javax.servlet.http.HttpServletRequest request, javax.servlet.http.HttpServletResponse response) throws javax.servlet.ServletException, IOException {
  
          //1.获得请求参数
          request.setCharacterEncoding("utf-8");
          String name = request.getParameter("name");
          String age = request.getParameter("age");
          System.out.println(name + ":" + age);
  
          // 2.响应数据给客户端（json数据）
          User user = new User("xiaoming", 18, "female");
          String jsonString = JSON.toJSONString(user);
  
          // 3.设置响应类型和响应内容
          response.setContentType("application/json;charset=utf-8");
          response.getWriter().println(jsonString);
  
      }
  
      protected void doGet(javax.servlet.http.HttpServletRequest request, javax.servlet.http.HttpServletResponse response) throws javax.servlet.ServletException, IOException {
          doPost(request, response);
      }
  }
  ```

## 4.2 案例练习

案例: 当页面加载好之后,从服务器上请求联系人数据,显示到网页上

普通版：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>异步请求案例</title>
    <script src="js/vue.js"></script>
    <script src="js/axios-0.18.0.js"></script>

</head>
<body>
<div id="div">
    <div id="users">
        <ul>
         <li v-for="element in users">
            id:{{element.id}} --name:{{element.name}}--age:{{element.age}}
         </li>
        </ul>
    </div>
</div>

</body>
<script>
    // 1.浏览器获得页面--从服务器获取页面
    // 2.浏览器加载页面时，创建Vue实例对象
    // 3.Vue实例对象创建完毕后会调用生命周期回调函数 created
    // 4.生命周期回调函数 created执行方法findAllUsres
    // 5.方法findAllUsres发送ajax请求获得数据，并修改Vue数据模型users数据
    // 6.Vue接管视图区域#div，并将里面的Vue指令和插值表达式进行解析，显示最终的数据
    new Vue({
        el:"#div",
        data:{
            users:[]
        },
        methods:{
            findAllUsres(){
                // 发送ajax请求来获得数据，并给Vue数据模型users赋值
                // axios
                axios.get("../FindAllServlet").then(resp=>{
                    // resp 是axios对响应内容的封装
                    // resp.data 就可以获得服务端的数据
                    console.log(resp);
                    //数据模型users赋值
                    this.users = resp.data;
                })
            }
        },
        created(){
            this.findAllUsres();
        }
    });
</script>
</html>
```



增强版：

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>异步请求案例</title>
    <script src="js/vue.js"></script>
    <script src="js/axios-0.18.0.js"></script>

</head>
<body>
<div id="div">
    <div id="users">
        <ul>
         <li v-for="element in users">
            id:{{element.id}} --name:{{element.name}}--age:{{element.age}}
         </li>
        </ul>
    </div>
</div>


</body>
<script>
    // 1.浏览器获得页面--从服务器获取页面
    // 2.浏览器加载页面时，创建Vue实例对象
    // 3.Vue实例对象创建完毕后会调用生命周期回调函数 created
    // 4.生命周期回调函数 created执行方法findAllUsres
    // 5.方法findAllUsres发送ajax请求获得数据，并修改Vue数据模型users数据
    // 6.Vue接管视图区域#div，并将里面的Vue指令和插值表达式进行解析，显示最终的数据
    new Vue({
        el:"#div",
        data:{
            users:[],
            params:{
                name:"xiaohaohao",
                age:88,
                gender:"",

            }
        },
        methods:{
            findAllUsres(){
                // 发送ajax请求来获得数据，并给Vue数据模型users赋值
                // axios
                // axios.get("../FindAllServlet").then(resp=>{
                //     // resp 是axios对响应内容的封装
                //     // resp.data 就可以获得服务端的数据
                //     console.log(resp);
                //     //数据模型users赋值
                //     this.users = resp.data;
                // })

                // 方式一：name=value&name=value
                // axios.post("../FindAllServlet","name=xiaoxiao&age=18").then(resp=>{
                //     // resp 是axios对响应内容的封装
                //     // resp.data 就可以获得服务端的数据
                //     console.log(resp);
                //     //数据模型users赋值
                //     this.users = resp.data;
                // })

                // 方式二：js对象（json对象）  axios默认不能传递js对象
                // axios.post("../FindAllServlet",`name=${this.params.name}&age=${this.params.age}`).then(resp=>{
                //     // resp 是axios对响应内容的封装
                //     // resp.data 就可以获得服务端的数据
                //     console.log(resp);
                //     //数据模型users赋值
                //     this.users = resp.data;
                // })

                // 设置axios参数，就可以传递js对象数据
                // axios.get()     $.get()
                // axios.post()    $.post()    --$.ajax
                /*
                       axios参数：
                        url--请求的路径
                        method--请求方式
                        heards--设置请求头信息
                        params--设置请求参数
                 */
                axios({
                    url:"../FindAllServlet",
                    method:"post",
                    heards:{"content-type":"application/x-www-form-urlencoded"},
                    params:this.params
                }).then(resp=>{
                    // resp 是axios对响应内容的封装
                    // resp.data 就可以获得服务端的数据
                    console.log(resp);
                    //数据模型users赋值
                    this.users = resp.data;
                })

            }
        },
        created(){
            this.findAllUsres();
        }
    });
</script>
</html>
```



















```java
package com.itheima.web.servlet;

import com.alibaba.fastjson.JSON;
import com.itheima.domain.User;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Collections;

/**
 * <p></p>
 *
 * @Description:
 */
@WebServlet("/FindAllServlet")
public class FindAllServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        // 1.构造数据
        User user1 = new User(1, "xiaoming", 18);
        User user2 = new User(2, "xiaohong", 18);
        User user3 = new User(3, "xiaoyu", 18);
        User user4 = new User(4, "xiaojinbao", 18);
        User user5 = new User(5, "xiaohaohao", 18);

        ArrayList<User> users = new ArrayList<User>();
        Collections.addAll(users, user1, user2, user3, user4, user5);

        // 2.将数据转为json格式
        String jsonString = JSON.toJSONString(users);

        // 3.设置响应类型和内容
        response.setContentType("application/json;charset=utf-8");
        response.getWriter().println(jsonString);


    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request, response);
    }
}
```



```java
package com.itheima03.axios;

import java.io.Serializable;

public class User implements Serializable {
    private Integer id;
    private String name;
    private String password;

    public User() {
    }

    public User(Integer id, String name, String password) {
        this.id = id;
        this.name = name;
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", password='" + password + '\'' +
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

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}

```





















# 5.设置Vue提示

为了在html页面中编写 Vue 代码有提示，需要在 IDEA 中进行相关设置，如下：

**1.在项目Setting中找到对应的设置**

![1599647271897](/img/1599647271897.png)



**2.将下面的命令填写到执行的位置**

```makefile
@tap,@tap.stop,@tap.prevent,@tap.once,@click,@click.stop,@click.prevent,@click.once,@change,@change.lazy,@change.number,@change.trim,@on,@blur,@fous,@submit,scoped,v-model,v-model.number,v-model.trim,v-for,v-text,v-html,v-show,v-if,v-else-if,v-else,v-pre,v-bind,v-bind:class,v-bind:style,v-bind:id,v-bind:href,:class,:style,:id,:href
```

![1599647212374](/img/1599647212374.png)