
# 1. 搜索技术   

搜索技术在我们日常生活的方方面面都会用到，例如：

- 综合搜索网站：百度、谷歌等
- 电商网站：京东、淘宝的商品搜索
- 软件内数据搜索：我们用的开发工具，如Idea的搜索功能

这些搜索业务有一些可以使用数据库来完成，有一些却不行。因此我们今天会学习一种新的搜索方案，解决海量数据、复杂业务的搜索。

## 1.1.关系型数据库搜索的问题

要实现类似百度的复杂搜索，或者京东的商品搜索，如果使用传统的数据库存储数据，那么会存在一系列的问题：

- 性能瓶颈：当数据量越来越大时，数据库搜索的性能会有明显下降。虽然可以通过分库分表来解决存储问题，但是性能问题并不能彻底解决，而且系统复杂度会提高、可用性下降。
- 复杂业务：百度或京东的搜索往往需要复杂的查询功能，例如：拼音搜索、错字的模糊搜索等。这些功能用数据库搜索难以实现，或者实现复杂度较高
- 并发能力：数据库是磁盘存储，虽然也有缓存方案，但是并不实用。因此数据库的读写并发能力较差，难以应对高并发场景

但是，并不是说数据库就一无是处。在一些对业务有强数据一致性需求，事物需求的情况下，数据库是不可替代的。

只是在海量数据的搜索方面，需要有新的技术来解决，就是我们今天要学习的**倒排索引**技术。

## 1.2.倒排索引

倒排索引是一种特殊的数据索引方式，虽然于数据库存储的数据结构没有太大差别，但是在检索方式上却大不一样。

### 1.2.1.基本概念

先来看两个概念：

- 文档（Document）：用来检索的海量数据，其中的每一条数据就是一个文档。例如一个网页、一个商品信息
- 词条（Term）：对文档数据或用户搜索数据，利用某种算法分词，得到的具备含义的词语就是词条。



例如，数据库中有下面的数据：

|  id  |                   title                    | url                          |
| :--: | :----------------------------------------: | ---------------------------- |
|  10  |          谷歌地图之父跳槽FaceBook          | http://www.itcast.cn/10.html |
|  20  |          谷歌地图之父加盟FaceBook          | http://www.itcast.cn/20.html |
|  30  |   谷歌地图创始人拉斯离开谷歌加盟Facebook   | http://www.itcast.cn/30.html |
|  40  | 谷歌地图之父跳槽Facebook与Wave项目取消有关 | http://www.itcast.cn/40.html |
|  50  |    谷歌地图之父拉斯加盟社交网站Facebook    | http://www.itcast.cn/50.html |

那么这里的每一行数据就是一条文档，如：

|  id  |          title           | url                          |
| :--: | :----------------------: | ---------------------------- |
|  10  | 谷歌地图之父跳槽FaceBook | http://www.itcast.cn/10.html |

把标题字段分词，可以得到词语如：`谷歌`就是一个词条



现在，假设用户要搜索`"谷歌创始人跳槽"`，来看看倒排索引的传统查找在检索时的区别：

### 1.2.1.传统查找流程

因为复杂搜索往往是模糊的查找，因此数据库索引基本都会实效，只能逐条数据判断。基本流程如下：

1）用户搜索数据，条件是title符合`"谷歌创始人跳槽"`

2）逐行获取数据，比如id为10的数据

3）判断数据中的title是否符合用户搜索条件

4）如果符合则放入结果集，不符合则丢弃。回到步骤1

如图：

![image-20200103154627038](assets/image-20200103154627038.png)

如果有5条数据，则需要遍历并判断5次。如果有100万数据，则需要循环遍历和判断100万次。线性查找和判断，效率极差，一个10mb的硬盘文件，遍历一遍需要3秒。



### 1.2.2.倒排索引流程

倒排索引的数据存储方式与数据库类似，但检索方式不同。

#### 1.2.2.1.数据存储方式

先看看倒排索引如何处理数据。

**文档列表**：

首先，倒排索引需要把文档数据逐个编号（从0递增），存储到文档表中。并且给每一个编号创建索引，这样根据编号检索文档的速度会非常快。

![image-20200103160217964](assets/image-20200103160217964.png) 

**词条列表（Term Dictionary）：**

然后，对文档中的数据按照算法做分词，得到一个个的词条，记录词条和词条出现的文档的编号、位置、频率信息，如图：

![image-20200103161448460](assets/image-20200103161448460.png) 

然后给词条创建索引，这样根据词条匹配和检索的速度就非常快。



#### 1.2.2.2.检索数据过程

倒排索引的检索流程如下：

1）用户输入条件`"谷歌创始人跳槽"`进行搜索。

2）对用户输入内容分词，得到词条：`谷歌`、`创始人`、`跳槽`。

3）拿着词条到词条列表中查找，可以得到包含词条的文档编号：0、1、2、3、4。

4）拿着词条的编号到文档列表中查找具体文档。

如图：

![image-20200103163447165](assets/image-20200103163447165.png)

虽然搜索会在两张表进行，但是每次都是根据索引查找，因此速度比传统搜索时的全表扫描速度要快的多。



## 1.3.Lucene

在java语言中，对倒排索引的实现中最广为人知的就是Lucene了，目前主流的java搜索框架都是依赖Lucene来实现的。

- Lucene是一套用于全文检索和搜寻的开源程序库，由Apache软件基金会支持和提供
- Lucene提供了一个简单却强大的应用程序接口（API），能够做全文索引和搜寻，在Java开发环境里Lucene是一个成熟的免费开放源代码工具
- Lucene并不是现成的搜索引擎产品，但可以用来制作搜索引擎产品，比较知名的搜索产品有：Solr、ElasticSearch    
- 官网：[http://lucene.apache.org](http://lucene.apache.org/)[/](http://lucene.apache.org/)



企业生产中一般都会使用成熟的搜索产品，例如：Solr或者Elasticsearch，不过从性能来看Elasticsearch略胜一筹，因此我们今天的学习目标就是elasticsearch。



# 2.ElasticSearch介绍和安装

如果把Lucene比喻成一台发动机，那么Solr就是一台家用汽车，而Elasticsearch就是一台c超级跑车。

## 2.1.简介

Elastic官网：https://www.elastic.co/cn/

Elastic是一系列产品的集合，比较知名的是ELK技术栈，其核心就是ElasticSearch：

Elasticsearch官网：https://www.elastic.co/cn/products/elasticsearch

![1526464283575](assets/1526464283575.png)

Elasticsearch是一个基于Lucene的搜索**web服务**，对外提供了一系列的Rest风格的API接口。因此任何语言的客户端都可以**通过发送Http请求来实现ElasticSearch的操作**。

ES的主要优势特点如下：

- **速度快**：Elasticsearch 很快，快到不可思议。我们通过有限状态转换器实现了用于全文检索的倒排索引，实现了用于存储数值数据和地理位置数据的 BKD 树，以及用于分析的列存储。而且由于每个数据都被编入了索引，因此您再也不用因为某些数据没有索引而烦心。您可以用快到令人惊叹的速度使用和访问您的所有数据。实现**近实时搜索**，海量数据更新在Elasticsearch中几乎是完全同步的。
- **扩展性高**：可以在笔记本电脑上运行，也可以在承载了 PB 级数据的成百上千台服务器上运行。原型环境和生产环境可无缝切换；无论 Elasticsearch 是在一个节点上运行，还是在一个包含 300 个节点的集群上运行，您都能够以相同的方式与 Elasticsearch 进行通信。它能够**水平扩展**，每秒钟可处理海量事件，同时能够自动管理索引和查询在集群中的分布方式，以实现极其流畅的操作。天生的分布式设计，很容易搭建大型的**分布式集群**（solr使用Zookeeper作为注册中心来实现分布式集群）
- **强大的查询和分析**：通过 Elasticsearch，您能够执行及合并多种类型的搜索（结构化数据、非结构化数据、地理位置、指标），搜索方式随心而变。先从一个简单的问题出发，试试看能够从中发现些什么。找到与查询最匹配的 10 个文档是一回事。但如果面对的是十亿行日志，又该如何解读呢？Elasticsearch 聚合让您能够从大处着眼，探索数据的趋势和模式。如全文检索，同义词处理，相关度排名，复杂数据分析。
- **操作简单**：客户端API支持Restful风格，简单容易上手。



使用ES的案例场景：

1、GitHub抛弃了Solr，采取ElasticSearch 来做PB级的搜索。 “GitHub使用ElasticSearch搜索20TB 的数据，包括13亿文件和1300亿行代码”

2、维基百科：启动以elasticsearch为基础的核心搜索架构

3、SoundCloud：“SoundCloud使用ElasticSearch为1.8亿用户提供即时而精准的音乐搜索服务”

4、百度：百度目前广泛使用ElasticSearch作为文本数据分析，采集百度所有服务器上的各类指标数据及用户自定义数据，通过对各种数据进行多维分析展示，辅助定位分析实例异常或业务层面异常。目前覆盖百度内部20多个业务线（包括casio、云分析、网盟、预测、文库、直达号、钱包、风控等），单集群最大100台机器，200个ES节点，每天导入30TB+数据。 新浪使用ES 分析处理32亿条实时日志。

5、阿里使用ES 构建自己的日志采集和分析体系 

6、京东到家订单中心 Elasticsearch 演进历程 

Elasticsearch 做为一款功能强大的分布式搜索引擎，支持近实时的存储、搜索数据，在京东到家订单系统中发挥着巨大作用，目前订单中心ES集群存储数据量达到10亿个文档，日均查询量达到5亿。

7、滴滴 2016 年初开始构建 Elasticsearch 平台，如今已经发展到超过 3500+ Elasticsearch 实例，超过 5PB 的数据存储，峰值写入 tps 超过了 2000w/s 的超大规模。

8、携程Elasticsearch应用案例

9、去哪儿：订单中心基于elasticsearch 的解决方案

。。。。。。。



目前Elasticsearch最新的版本是7.x.x，我们就使用7.4.2版本，在课前资料中可以找到安装包:

![](assets/image-20200103101914300.png) 

需要JDK1.8及以上（在7.0以后的版本中，自带了jdk）

如果要自己下载，地址：https://mirrors.huaweicloud.com/elasticsearch/



## 2.2.安装

我们选择在windows下安装。

安装ES的步骤非常简单：

- 解压：绿色版软件，解压即可使用
- 配置：修改ES的配置文件中部分属性
- 启动：运行启动脚本即可启动

### 1）解压

把课前资料提供的文件解压到某个目录即可，最好不要有中文：

![](assets/image-20200103095021725.png) 

目录结构如下：

![](assets/image-20200103095410105.png) 

### 2）修改配置

进入config目录，有下面的配置：

![](assets/image-20200103095508370.png) 

核心是两个配置：

![](assets/image-20200103095601575.png) 

我们打开jvm.options，修改JVM内存参数：

Elasticsearch基于Lucene的，而Lucene底层是java实现，因此我们需要配置jvm参数

默认配置如下：

```
-Xms1g
-Xmx1g
```

内存占用太多了，我们调小一些：

```
-Xms512m
-Xmx512m
```

### 3）启动

进入安装目录的bin目录：

![](assets/image-20200103100046847.png) 

双击elasticsearch.bat文件即可启动。可以看到日志中的IP和端口信息：

![](assets/image-20200103100250523.png)

在浏览器中访问：http://127.0.0.1:9200

![](assets/image-20200103100431337.png) 



## 2.3.Kibana

ES提供了多种访问方式，官方推荐使用REST API,也是使用人数最多的访问方式。就是通过http协议，使用Restful的风格，按照es的api去操作数据，访问时我们需要传递给es json 参数,es处理后会给我们返回 json 的结果,不过浏览器不方便操作es 官方推荐使用kibana来操作ES

### 2.3.1.什么是Kibana？

![1526481256534](assets/1526481256534.png)

Kibana是一个基于Node.js的Elasticsearch索引库数据统计工具，可以利用Elasticsearch的聚合功能，生成各种图表，如柱形图，线状图，饼图等。

而且还提供了操作Elasticsearch索引数据的控制台，并且提供了一定的API提示，非常有利于我们学习Elasticsearch的语法。

### 2.3.2.安装NodeJS

因为Kibana依赖于node，需要在windows下先安装Node.js，双击运行课前资料提供的node.js的安装包：

![1555583973406](assets/1555583973406.png) 

一路下一步即可安装成功，然后在任意黑窗口输入名：

```
node -v
```

可以查看到node版本，如下：

![1555584029808](assets/1555584029808.png) 

如果安装后，命令没有反应，可以重启电脑再试。



### 2.3.3.安装Kibana

课前资料提供了Kibana的安装包：

![](assets/image-20200103100818438.png) 

#### 1）解压

kibana同样是绿色版，解压即可使用：

![](assets/image-20200103100919509.png) 

目录结构：

![](assets/image-20200103101100541.png) 

#### 2）运行

进入bin目录：

![](assets/image-20200103101202949.png) 

双击kibana.bat文件即可

双击运行：

![1526482862080](assets/1526482862080.png) 

发现kibana的监听端口是5601

我们访问：http://127.0.0.1:5601

![image-20200103180202525](assets/image-20200103180202525.png)

#### 3）控制台

Kibana提供了开发工具(devTools)方便我们操作ES，点击左侧菜单即可找到：

![image-20200103180318017](assets/image-20200103180318017.png) 

在控制台中，可以方便的向ES发起Http请求：

![1526483200872](assets/1526483200872.png)

说明：

- `GET`：代表是发送了一个`GET`方式的http请求
- `_search`：是请求的URL路径，前面省略了`localhost:9200`
- `{"query": { "match_all": {} } }`：是JSON风格的请求参数，这里代表查询所有数据

## 2.4.分词器

根据我们之前讲解的倒排索引原理，当我们向elasticsearch插入一条文档数据时，elasticsearch需要对数据分词，分词到底如何完成呢？

### 2.4.1.默认的分词器

kibana中可以测试分词器效果，我们来看看elasticsearch中的默认分词器。

在kibana的DevTools中输入一段命令：

```
POST /_analyze
{
  "text": "黑马程序员学习java太棒了",
  "analyzer": "standard"
}
```

请求代表的含义：

- 请求方式：`POST`
- 请求路径：`_analyze`，前面省略了http://127.0.0.1:9200，这个由Kibana帮我们补充
- 请求参数：风格，包含两个属性：
  - `analyzer`：分词器名称，standard是默认的标准分词器
  - `text`：要分词的文本内容



效果：

```
{
  "tokens" : [
    {
      "token" : "黑",
      "start_offset" : 0,
      "end_offset" : 1,
      "type" : "<IDEOGRAPHIC>",
      "position" : 0
    },
    {
      "token" : "马",
      "start_offset" : 1,
      "end_offset" : 2,
      "type" : "<IDEOGRAPHIC>",
      "position" : 1
    },
    {
      "token" : "程",
      "start_offset" : 2,
      "end_offset" : 3,
      "type" : "<IDEOGRAPHIC>",
      "position" : 2
    },
    {
      "token" : "序",
      "start_offset" : 3,
      "end_offset" : 4,
      "type" : "<IDEOGRAPHIC>",
      "position" : 3
    },
    {
      "token" : "员",
      "start_offset" : 4,
      "end_offset" : 5,
      "type" : "<IDEOGRAPHIC>",
      "position" : 4
    },
    {
      "token" : "学",
      "start_offset" : 5,
      "end_offset" : 6,
      "type" : "<IDEOGRAPHIC>",
      "position" : 5
    },
    {
      "token" : "习",
      "start_offset" : 6,
      "end_offset" : 7,
      "type" : "<IDEOGRAPHIC>",
      "position" : 6
    },
    {
      "token" : "java",
      "start_offset" : 7,
      "end_offset" : 11,
      "type" : "<ALPHANUM>",
      "position" : 7
    },
    {
      "token" : "太",
      "start_offset" : 11,
      "end_offset" : 12,
      "type" : "<IDEOGRAPHIC>",
      "position" : 8
    },
    {
      "token" : "棒",
      "start_offset" : 12,
      "end_offset" : 13,
      "type" : "<IDEOGRAPHIC>",
      "position" : 9
    },
    {
      "token" : "了",
      "start_offset" : 13,
      "end_offset" : 14,
      "type" : "<IDEOGRAPHIC>",
      "position" : 10
    }
  ]
}

```

一个字分成一个词，实在是太糟糕了。。

### 2.4.2.中文分词

标准分词器并不能很好处理中文，一般我们会用第三方的分词器，例如：IK分词器。

IK分词器的 地址：https://github.com/medcl/elasticsearch-analysis-ik， 安装非常简单。

把课前资料中的zip包：

![](assets/image-20200103101914300-1590741940714.png) 

解压到Elasticsearch目录的plugins目录中，并重命名为ik：

![](assets/image-20200103102056432.png) 

然后重启elasticsearch：

![1526523386610](assets/1526523386610.png)

IK分词器加载成功了！

再次运行命令，不过把分词器换成IK：

```
POST /_analyze
{
  "text": "黑马程序员学习java太棒了",
  "analyzer": "ik_smart"
}
```

IK分词器可以用ik_max_word和ik_smart两种方式，分词粒度不同。

效果：

```
{
  "tokens" : [
    {
      "token" : "黑马",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "程序员",
      "start_offset" : 2,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 1
    },
    {
      "token" : "学习",
      "start_offset" : 5,
      "end_offset" : 7,
      "type" : "CN_WORD",
      "position" : 2
    },
    {
      "token" : "java",
      "start_offset" : 7,
      "end_offset" : 11,
      "type" : "ENGLISH",
      "position" : 3
    },
    {
      "token" : "太棒了",
      "start_offset" : 11,
      "end_offset" : 14,
      "type" : "CN_WORD",
      "position" : 4
    }
  ]
}
```



# 3.快速入门

接下来快速看下elasticsearch的使用

## 3.1.概念

Elasticsearch虽然是一种NoSql库，但最终的目的是存储数据、检索数据。因此很多概念与MySQL类似的。

| ES中的概念       | 数据库概念         | 说明                                                         |
| ---------------- | ------------------ | ------------------------------------------------------------ |
| 索引库（indices) | 数据库（Database） | ES中可以有多个索引库，就像Mysql中有多个Database一样。        |
| 类型             | 表（table）        | mysql中database可以有多个table，table用来约束数据结构。而ES中的每个索引库中只有一个`类型`，`类型`中用来约束字段属性的叫做映射(`mapping`) |
| 映射（mappings） | 表的字段约束       | mysql表对字段有约束，ES中叫做映射，用来约束字段属性，包括：字段名称、数据类型等信息 |
| 文档（document） | 行（Row）          | 存入索引库原始的数据，比如每一条商品信息，就是一个文档。对应mysql中的每行数据 |
| 字段（field）    | 列（Column）       | 文档中的属性，一个文档可以有多个属性。就像mysql中一行数据可以有多个列。 |

因此，我们对ES的操作，就是对索引库、类型映射、文档数据的操作：

- 索引库操作：主要包含创建索引库、查询索引库、删除索引库等
- 类型映射操作：主要是创建类型映射、查看类型映射
- 文档操作：文档的新增、修改、删除、查询

### Rest的API介绍

操作MySQL，主要是database操作、表操作、数据操作，对应在elasticsearch中，分别是对索引库操作、类型映射操作、文档数据的操作：

- 索引库操作：主要包含创建索引库、查询索引库、删除索引库等
- 类型映射操作：主要是创建类型映射、查看类型映射
- 文档操作：文档的新增、修改、删除、查询

而ES中完成上述操作都可以通过Rest风格的API来完成，符合请求要求的Http请求就可以完成数据的操作，详见官方文档：https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html

下面我们分别来学习。



# 4.ES的索引库操作

按照Rest风格，增删改查分别使用：POST、DELETE、PUT、GET等请求方式，路径一般是资源名称。因此索引库操作的语法类似。

## 4.1.创建索引库

创建索引库的请求格式：

- 请求方式：PUT

- 请求路径：/索引库名

- 请求参数：格式：

  ```
  {
      "settings": {
          "属性名": "属性值"
      }
  }
  ```

  settings：就是索引库设置，其中可以定义索引库的各种属性，目前我们可以不设置，都走默认。

示例：

```
put /索引库名
{
    "settings": {
        "属性名": "属性值"
      }
}
```



在Kibana中测试一下：

![](assets/image-20200103111100436.png) 

这里我们没有写settings属性，索引库配置都走默认。



## 4.2.查询索引库

Get请求可以帮我们查看索引信息，格式：

```
GET /索引库名
```

在Kibana中测试一下：

![](assets/image-20200103111317109.png)

可以看到返回的信息也是JSON格式，其中包含这么几个属性：

- mappings：类型映射，目前我们没有给索引库设置映射
- settings：索引库配置，目前是默认配置

## 4.3.删除索引库

删除索引使用DELETE请求，格式：

```
DELETE /索引库名
```

在Kibana中测试一下：

![](assets/image-20200103111453582.png) 



## 4.4.总结

索引库操作：

- 创建索引库： `PUT /库名称`
- 查询索引库： `GET /索引库名称`
- 删除索引库： `DELETE /索引库名称`



# 5.类型映射

MySQL中有表，并且表中有对字段的约束，对应到elasticsearch中就是类型映射`mapping`.



## 5.1.映射属性

索引库数据类型是松散的，不过也需要我们指定具体的字段及字段约束信息。而约束字段信息的就叫做映射（`mapping`）。

映射属性包括很多：

![image-20200529170444227](assets/image-20200529170444227.png) 

参考官网：https://www.elastic.co/guide/en/elasticsearch/reference/7.x/mapping-params.html

elasticsearch字段的映射属性该怎么选，除了字段名称外，我们一般要考虑这样几个问题：

- 1）数据的类型是什么？
  - 这个比较简单，根据字段的含义即可知道，可以通过`type`属性来指定

- 2）数据是否参与搜索？
  - 参与搜索的字段将来需要创建倒排索引，作为搜索字段。可以通过`index`属性来指定是否参与搜索，默认为true，也就是每个字段都参与搜索

- 3）数据存储时是否需要分词？
  - 一个字段的内容如果不是一个不可分割的整体，例如国家，一般都需要分词存储。

- 4）如果分词的话用什么分词器？
  - 分词器类型很多，中文一般选择IK分词器
  - 指定分词器类型可以通过`analyzer`属性指定

## 5.2.数据类型

elasticsearch提供了非常丰富的数据类型：

![image-20200529170805336](assets/image-20200529170805336.png)

比较常见的有：

- string类型，又分两种：
  - text：可分词，存储到elasticsearch时会根据分词器分成多个词条
  - keyword：不可分词，数据会完整的作为一个词条
- Numerical：数值类型，分两类

  - 基本数据类型：long、interger、short、byte、double、float、half_float
  - 浮点数的高精度类型：scaled_float
    - 需要指定一个精度因子，比如10或100。elasticsearch会把真实值乘以这个因子后存储，取出时再还原。
- Date：日期类型

- Object：对象，对象不便于搜索。因此ES会把对象数据扁平化处理再存储。





## 5.3.创建类型映射

我们可以给一个已经存在的索引库添加映射关系，也可以创建索引库的同时直接指定映射关系。

### 5.3.1.索引库已经存在

我们假设已经存在一个索引库，此时要给索引库添加映射。

> 语法

请求方式依然是PUT

```
PUT /索引库名/_mapping
{
  "properties": {
    "字段名1": {
      "type": "类型",
      "index": true，
      "analyzer": "分词器"
    },
    "字段名2": {
      "type": "类型",
      "index": true，
      "analyzer": "分词器"
    },
    ...
  }
}
```

- 类型名称：就是前面将的type的概念，类似于数据库中的表
  字段名：任意填写，下面指定许多属性，例如：
  - type：类型，可以是text、long、short、date、integer、object等
  - index：是否参与搜索，默认为true
  - analyzer：分词器

> 示例

发起请求：

```
PUT /heima/_mapping
{
  "properties": {
    "title": {
      "type": "text",
      "analyzer": "ik_max_word"
    },
    "images": {
      "type": "keyword",
      "index": "false"
    },
    "price": {
      "type": "float"
    }
  }
}
```

响应结果：

```
{
  "acknowledged": true
}
```

上述案例中，就给heima这个索引库中设置了3个字段：

- title：商品标题 
  - index：标题一般都参与搜索，所以index没有配置，按照默认为true
  - type: 标题是字符串类型，并且参与搜索和分词，所以用text
  - analyzer：标题内容一般较多，查询时需要分词，所以指定了IK分词器
- images：商品图片
  - type：图片是字符串，并且url不需要分词，所以用keyword
  - index：图片url不参与搜索，所以给false
- price：商品价格
  - type：价格，浮点型，这里给了float
  - index：这里没有配置，默认是true，代表参与搜索

并且给这些字段设置了一些属性：

### 5.3.2.索引库不存在

如果一个索引库是不存在的，我们就不能用上面的语法，而是这样：

```
PUT /索引库名
{
	"mappings":{
      "properties": {
        "字段名1": {
          "type": "类型",
          "index": true，
          "analyzer": "分词器"
        },
        "字段名2": {
          "type": "类型",
          "index": true，
          "analyzer": "分词器"
        },
        ...
      }
    }
}
```

示例：

```
# 创建索引库和映射
PUT /heima2
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "ik_max_word"
      },
      "images": {
        "type": "keyword",
        "index": "false"
      },
      "price": {
        "type": "float"
      }
    }
  }
}
```

结果：

```
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "heima2"
}
```

## 5.4.查看映射关系

查看使用Get请求

> 语法：

```
GET /索引库名/_mapping
```

> 示例：

```
GET /heima/_mapping
```

> 响应：

```
{
  "heima" : {
    "aliases" : { },
    "mappings" : {
      "properties" : {
        "images" : {
          "type" : "keyword",
          "index" : false
        },
        "price" : {
          "type" : "float"
        },
        "title" : {
          "type" : "text",
          "analyzer" : "ik_max_word"
        }
      }
    },
    "settings" : {
      "index" : {
        "creation_date" : "1590744589271",
        "number_of_shards" : "1",
        "number_of_replicas" : "1",
        "uuid" : "v7AHmI9ST76rHiCNHb5KQg",
        "version" : {
          "created" : "7040299"
        },
        "provided_name" : "heima"
      }
    }
  }
}
```





# 6.文档的操作

我们把数据库中的每一行数据查询出来，存入索引库，就是文档。文档的主要操作包括新增、查询、修改、删除。

## 6.1.新增文档

### 6.1.1.新增并随机生成id

通过POST请求，可以向一个已经存在的索引库中添加文档数据。ES中，文档都是以JSON格式提交的。

> 语法：

```
POST /{索引库名}/_doc
{
    "key":"value"
}
```

> 示例：

```
# 新增文档数据
POST /heima/_doc
{
    "title":"小米手机",
    "images":"http://image.leyou.com/12479122.jpg",
    "price":2699.00
}
```

> 响应：

```
{
  "_index" : "heima",
  "_type" : "_doc",
  "_id" : "rGFGbm8BR8Fh6kyTbuq8",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

结果解释：

- `_index`：新增到了哪一个索引库。
- `_id`：这条文档数据的`唯一标示`，文档的**增删改查**都依赖这个id作为唯一标示。此处是由ES随即生成的，我们也可以指定使用某个ID
- `result`：执行结果，可以看到结果显示为：`created`，说明文档创建成功。



### 6.1.1.新增并指定id

通过POST请求，可以向一个已经存在的索引库中添加文档数据。ES中，文档都是以JSON格式提交的。

> 语法：

```
POST /{索引库名}/_doc/{id}
{
    "key":"value"
}
```

> 示例：

```
# 新增文档数据并指定id
POST /heima/_doc/1
{
    "title":"小米手机",
    "images":"http://image.leyou.com/12479122.jpg",
    "price":2699.00
}
```

> 响应：

```
{
  "_index" : "heima",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}
```

同样新增成功了！



## 6.2.查看文档

根据rest风格，新增是post，查询应该是get，不过查询一般都需要条件，这里我们把刚刚生成数据的id带上。

语法：

```
GET /{索引库名称}/_doc/{id}
```

通过kibana查看数据：

```js
GET /heima/_doc/rGFGbm8BR8Fh6kyTbuq8
```

查看结果：

```
{
  "_index": "heima",
  "_type": "goods",
  "_id": "r9c1KGMBIhaxtY5rlRKv",
  "_version": 1,
  "found": true,
  "_source": {
    "title": "小米手机",
    "images": "http://image.leyou.com/12479122.jpg",
    "price": 2699
  }
}
```

- `_source`：源文档信息，所有的数据都在里面。

## 6.3.修改文档

把刚才新增的请求方式改为PUT，就是修改了。不过修改必须指定id，

- id对应文档存在，则修改
- id对应文档不存在，则新增

比如，我们把使用id为3，不存在，则应该是新增：

```
# 新增
PUT /heima/_doc/2
{
    "title":"大米手机",
    "images":"http://image.leyou.com/12479122.jpg",
    "price":2899.00
}
```

结果：

```
{
  "_index" : "heima",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 2,
  "_primary_term" : 1
}
```

可以看到是`created`，是新增。

我们再次执行刚才的请求，不过把数据改一下：

```
# 修改
PUT /heima/_doc/2
{
    "title":"大米手机Pro",
    "images":"http://image.leyou.com/12479122.jpg",
    "price":3099.00
}
```

查看结果：

```
{
  "_index" : "heima",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 2,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 3,
  "_primary_term" : 1
}
```

可以看到结果是：`updated`，显然是更新数据



说明: 

es对API的要求并没有那么严格

如: 

- POST 新增文档时：如果指定了id，并且id在索引库不存在，直接存入索引库；如果id已经存在，则执行修改

- PUT修改文档时：先根据id删除指定文档，然后加入新的文档
- PUT修改文档时:  如果对应的文档不存在时，会添加该文档

## 6.4.删除文档

删除使用DELETE请求，同样，需要根据id进行删除：

> 语法

```
DELETE /索引库名/类型名/id值
```

> 示例

```
# 根据id删除数据
DELETE /heima/_doc/rGFGbm8BR8Fh6kyTbuq8
```

结果：

```
{
  "_index" : "heima",
  "_type" : "_doc",
  "_id" : "rGFGbm8BR8Fh6kyTbuq8",
  "_version" : 2,
  "result" : "deleted",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 4,
  "_primary_term" : 1
}
```

可以看到结果result为：deleted，说明删除成功了。

## 6.5.默认映射

刚刚我们在新增数据时，添加的字段都是提前在类型中通过mapping定义过的，如果我们添加的字段并没有提前定义过，能够成功吗？

事实上Elasticsearch有一套默认映射规则，如果新增的字段从未定义过，那么就会按照默认映射规则来存储。

测试一下：

```
# 新增未映射字段
POST /heima/_doc/3
{
    "title":"超大米手机",
    "images":"http://image.leyou.com/12479122.jpg",
    "price":3299.00,
    "stock": 200,
    "saleable":true,
    "subTitle":"超级双摄,亿级像素"
}
```

我们额外添加了stock库存，saleable是否上架，subtitle副标题、3个字段。

来看结果：

```
{
  "_index" : "heima",
  "_type" : "_doc",
  "_id" : "3",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 5,
  "_primary_term" : 1
}
```

成功了！在看下索引库的映射关系:

```
{
  "heima" : {
    "mappings" : {
      "properties" : {
        "images" : {
          "type" : "keyword",
          "index" : false
        },
        "price" : {
          "type" : "float"
        },
        "saleable" : {
          "type" : "boolean"
        },
        "stock" : {
          "type" : "long"
        },
        "subTitle" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        },
        "title" : {
          "type" : "text",
          "analyzer" : "ik_max_word"
        }
      }
    }
  }
}

```

stock、saleable、subtitle都被成功映射了:

- stock：默认映射为long类型
- saleable：映射为布尔类型
- subtitle是String类型数据，ES无法确定该用text还是keyword，它就会存入两个字段。例如：
  - subtitle：text类型
  - subtitle.keyword：keyword类型

如图：

![image-20200104102125708](assets/image-20200104102125708.png) 



## 6.6.动态映射模板

### 6.6.1.基本语法

默认映射规则不一定符合我们的需求，我们可以按照自己的方式来定义默认规则。这就需要用到动态模板了。

动态模板的语法：

![1547005993592](assets/1547005993592.png) 

- 模板名称，随便起
- 匹配条件，凡是符合条件的未定义字段，都会按照这个mapping中的规则来映射，匹配规则包括：
  - `match_mapping_type`：按照数据类型匹配，如：string匹配字符串类型，long匹配整型
  - `match`和`unmatch`：按照名称通配符匹配，如：`t_*`匹配名称以t开头的字段
- 映射规则，匹配成功后的映射规则

凡是映射规则中未定义，而符合2中的匹配条件的字段，就会按照3中定义的映射方式来映射



### 6.6.2.示例

在kibana中定义一个索引库，并且设置动态模板：

```
# 动态模板
PUT heima3 
{
  "mappings": {
      "properties": {
        "title": {
          "type": "text",
          "analyzer": "ik_max_word"
        }
      },
      "dynamic_templates": [
        {
          "strings": { 
            "match_mapping_type": "string",
            "mapping": {
              "type": "keyword"
            }
          }
        }
      ]
    }
}
```

这个动态模板的意思是：凡是string类型的自动，统一按照 keyword来处理。

接下来新增一个数据试试：

```
POST /heima2/_doc/1
{
    "title":"超大米手机",
    "images":"http://image.leyou.com/12479122.jpg",
    "price":3299.00
}
```

然后查看映射：

```
GET /heima2/_mapping
```

结果：

```
{
  "heima2" : {
    "mappings" : {
      "dynamic_templates" : [
        {
          "strings" : {
            "match_mapping_type" : "string",
            "mapping" : {
              "type" : "keyword"
            }
          }
        }
      ],
      "properties" : {
        "images" : {
          "type" : "keyword"
        },
        "price" : {
          "type" : "float"
        },
        "title" : {
          "type" : "text",
          "analyzer" : "ik_max_word"
        }
      }
    }
  }
}
```

可以看到images是一个字符串，被映射成了keyword类型。



# 7.查询

首先我们批量导入一些数据，方便后面的讲解：

```
# 删除heima索引库
DELETE /heima
# 重新创建
PUT /heima
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "ik_max_word"
      },
      "images": {
        "type": "keyword",
        "index": "false"
      },
      "price": {
        "type": "float"
      }
    }
  }
}
```

```
# 批量导入
POST _bulk
{"index":{"_index":"heima","_type":"_doc","_id":"1"}}
{"title":"三星 Note II (N7100) 云石白 联通3G手机","images":"http://image.leyou.com/12479122.jpg","price":2599.00}
{"index":{"_index":"heima","_type":"_doc","_id":"2"}}
{"title":"三星 B9120 钛灰色 联通3G手机 双卡双待双通","images":"http://image.leyou.com/12479122.jpg","price":2899.00}
{"index":{"_index":"heima","_type":"_doc","_id":"3"}}
{"title":"夏普（SHARP）LCD-46DS40A 46英寸 日本原装液晶面板 智能全高清液晶电视","images":"http://image.leyou.com/12479122.jpg","price":12880.00}
{"index":{"_index":"heima","_type":"_doc","_id":"4"}}
{"title":"中兴 U288 珠光白 移动3G手机","images":"http://image.leyou.com/12479122.jpg","price":1099.00}
{"index":{"_index":"heima","_type":"_doc","_id":"5"}}
{"title":"飞利浦 老人手机 (X2560) 喜庆红 移动联通2G手机 双卡双待","images":"http://image.leyou.com/12479122.jpg","price":500.00}
{"index":{"_index":"heima","_type":"_doc","_id":"6"}}
{"title":"诺基亚(NOKIA) 1050 (RM-908) 黑色 移动联通2G手机","images":"http://image.leyou.com/12479122.jpg","price":998.00}
{"index":{"_index":"heima","_type":"_doc","_id":"7"}}
{"title":"海信（Hisense）LED42EC260JD 42英寸 窄边网络 LED电视（黑色）","images":"http://image.leyou.com/12479122.jpg","price":3255.00}
{"index":{"_index":"heima","_type":"_doc","_id":"8"}}
{"title":"酷派 8076D 咖啡棕 移动3G手机 双卡双待","images":"http://image.leyou.com/12479122.jpg","price":1499.00}
{"index":{"_index":"heima","_type":"_doc","_id":"9"}}
{"title":"华为 P6 (P6-C00) 黑 电信3G手机 双卡双待双通", "images":"http://image.leyou.com/12479122.jpg","price":2340.00}
{"index":{"_index":"heima","_type":"_doc","_id":"10"}}
{"title":"康佳（KONKA） LED42K11A 42英寸 网络安卓智能液晶电视（黑色+银色）","images":"http://image.leyou.com/12479122.jpg","price":3699.00}
```

## 7.1.基本查询

elasticsearch提供的查询方式有很多，例如：

- 查询所有
- 分词查询
- 词条查询
- 模糊查询
- 范围查询
- 布尔查询



虽然查询的方式有很多，但是基本语法是一样的：

> 基本语法

```
GET /索引库名/_search
{
    "query":{
        "查询类型":{
            "查询条件":"查询条件值"
        }
    }
}
```

这里的query代表一个查询对象，里面可以有不同的查询属性

- 查询类型，有许多固定的值，如：
  - match_all：查询所有
  - match：分词查询
  - term：词条查询
  - fuzzy：模糊查询
  - range：范围查询
  - bool：布尔查询
- 查询条件：查询条件会根据类型的不同，写法也有差异，后面详细讲解

  **注意:  只有index为true的字段才可以作为查询条件哦~~**

### 7.1.1 查询所有match_all

> 示例：

```
# 查询所有 match_all
GET /heima/_search
{
    "query":{
        "match_all": {}
    }
}
```

- `query`：代表查询对象
- `match_all`：代表查询所有

> 结果：

```
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 10,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "title" : "三星 Note II (N7100) 云石白 联通3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2599
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "title" : "三星 B9120 钛灰色 联通3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2899
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "3",
        "_score" : 1.0,
        "_source" : {
          "title" : "夏普（SHARP）LCD-46DS40A 46英寸 日本原装液晶面板 智能全高清液晶电视",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 12880
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "4",
        "_score" : 1.0,
        "_source" : {
          "title" : "中兴 U288 珠光白 移动3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 1099
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "5",
        "_score" : 1.0,
        "_source" : {
          "title" : "飞利浦 老人手机 (X2560) 喜庆红 移动联通2G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 500
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "6",
        "_score" : 1.0,
        "_source" : {
          "title" : "诺基亚(NOKIA) 1050 (RM-908) 黑色 移动联通2G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 998
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "7",
        "_score" : 1.0,
        "_source" : {
          "title" : "海信（Hisense）LED42EC260JD 42英寸 窄边网络 LED电视（黑色）",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 3255
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "8",
        "_score" : 1.0,
        "_source" : {
          "title" : "酷派 8076D 咖啡棕 移动3G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 1499
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "9",
        "_score" : 1.0,
        "_source" : {
          "title" : "华为 P6 (P6-C00) 黑 电信3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2340
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "10",
        "_score" : 1.0,
        "_source" : {
          "title" : "康佳（KONKA） LED42K11A 42英寸 网络安卓智能液晶电视（黑色+银色）",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 3699
        }
      }
    ]
  }
}

```

- took：查询花费时间，单位是毫秒
- time_out：是否超时
- _shards：分片信息
- hits：搜索结果总览对象
  - total：搜索到的总条数
  - max_score：所有结果中文档得分的最高分
  - hits：搜索结果的文档对象数组，每个元素是一条搜索到的文档信息
    - _index：索引库
    - _type：文档类型
    - _id：文档id
    - _score：文档得分

    Elasticsearch 默认是按照文档与查询的相关度 (匹配度) 的得分倒序返回结果的. 得分 (_score) 就越大, 表示相关性越高. 

    - _source：文档的源数据



### 7.1.2 分词查询match

`match`类型查询，会把查询条件进行分词，然后进行查询,多个词条之间默认是or的关系

语法：

```
GET /heima/_search
{
    "query":{
        "match":{
            "字段名":"搜索条件"
        }
    }
}
```



示例：

```
GET /heima/_search
{
    "query":{
        "match":{
            "title":"三星手机"
        }
    }
}
```

结果：

```
{
  "took" : 5,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 7,
      "relation" : "eq"
    },
    "max_score" : 5.250329,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 5.250329,
        "_source" : {
          "title" : "三星 Note II (N7100) 云石白 联通3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2599
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 5.250329,
        "_source" : {
          "title" : "三星 B9120 钛灰色 联通3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2899
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "5",
        "_score" : 1.0063455,
        "_source" : {
          "title" : "飞利浦 老人手机 (X2560) 喜庆红 移动联通2G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 500
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "4",
        "_score" : 0.83515364,
        "_source" : {
          "title" : "中兴 U288 珠光白 移动3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 1099
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "6",
        "_score" : 0.8129092,
        "_source" : {
          "title" : "诺基亚(NOKIA) 1050 (RM-908) 黑色 移动联通2G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 998
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "8",
        "_score" : 0.8129092,
        "_source" : {
          "title" : "酷派 8076D 咖啡棕 移动3G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 1499
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "9",
        "_score" : 0.73464036,
        "_source" : {
          "title" : "华为 P6 (P6-C00) 黑 电信3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2340
        }
      }
    ]
  }
}

```

查询结果中把title 字段包含了 "三星" 或 "手机" 的数据都查询了处理，说明在默认情况下，

分词查询 将查询条件进行分词，并以 **or** 的关系，将所有满足条件的数据查了出来。



某些情况下，我们需要更精确查找，我们希望这个关系变成`and`，可以这样做：

```
GET /heima/_search
{
    "query":{
        "match":{
            "title":{"query":"三星手机","operator":"and"}
        }
    }
}
```

结果：

 ```
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 2,
      "relation" : "eq"
    },
    "max_score" : 5.250329,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 5.250329,
        "_source" : {
          "title" : "三星 Note II (N7100) 云石白 联通3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2599
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 5.250329,
        "_source" : {
          "title" : "三星 B9120 钛灰色 联通3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2899
        }
      }
    ]
  }
}
 ```

本例中，只有同时包含`三星`和`手机`的词条才会被搜索到。



### 7.1.3 词条匹配term

`term` 查询，词条查询。查询的条件是一个词条，不会被分词。可以是keyword类型的字符串、数值、或者text类型字段中分词得到的某个词条.

```
GET /heima/_search
{
    "query":{
        "term": {
          "price": 2599
        }
    }
}
```

结果：

```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "title" : "三星 Note II (N7100) 云石白 联通3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2599
        }
      }
    ]
  }
}
```

### 7.1.4.模糊查询fuzzy 

相似度查询

首先看一个概念，叫做编辑距离：一个词条变为另一个词条需要修改的次数，例如：

facebool 要修改成facebook 需要做的是把 l 修改成k ，一次即可，编辑距离就是1

模糊查询允许用户查询内容与实际内容存在偏差，但是编辑距离不能超过2，示例：



```
# 模糊查询 fuzzy
GET /heima/_search
{
    "query":{
        "fuzzy": {
          "title": {
            "value": "飞利铺",
            "fuzziness": 1
          }
        }
    }
}
```

本例中我搜索的是`飞利铺`，看看结果：

```
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.2439895,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "5",
        "_score" : 1.2439895,
        "_source" : {
          "title" : "飞利浦 老人手机 (X2560) 喜庆红 移动联通2G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 500
        }
      }
    ]
  }
}

```

可以看到结果中`飞利浦`还是被搜索到了。



### 7.1.5.范围查询range

`range` 查询找出那些落在指定区间内的数字或者时间

```
# 范围查询
GET /heima/_search
{
  "query": {
    "range": {
      "price": {
        "gte": 1000,
        "lt": 2800
      }
    }
  }
}
```

`range`查询允许以下字符：

| 操作符 |   说明   |
| :----: | :------: |
|   gt   |   大于   |
|  gte   | 大于等于 |
|   lt   |   小于   |
|  lte   | 小于等于 |

结果：

```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 4,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "title" : "三星 Note II (N7100) 云石白 联通3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2599
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "4",
        "_score" : 1.0,
        "_source" : {
          "title" : "中兴 U288 珠光白 移动3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 1099
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "8",
        "_score" : 1.0,
        "_source" : {
          "title" : "酷派 8076D 咖啡棕 移动3G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 1499
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "9",
        "_score" : 1.0,
        "_source" : {
          "title" : "华为 P6 (P6-C00) 黑 电信3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2340
        }
      }
    ]
  }
}
```

### 7.1.6.布尔查询

`bool`把各种其它查询通过`must`（与）、`must_not`（非）、`should`（或）的方式进行组合

```
# 想查找手机
# 想查找价格 2099 ~ 2999
# 不想用三星
GET /heima/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "手机"
          }
        },
        {
          "range": {
            "price": {
              "gte": 2099,
              "lte": 2999
            }
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "title": "三星"
          }
        }
      ]
    }
  }
}

```

结果：

```
{
  "took" : 7,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.7346404,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "9",
        "_score" : 1.7346404,
        "_source" : {
          "title" : "华为 P6 (P6-C00) 黑 电信3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2340
        }
      }
    ]
  }
}

```

三个关系规则：

- MUST：该规则必须有效
- MUST_NOT:该规则必须无效
- SHOULD:该规则可以有效。

常见组合：

- Must+Must：交集：结果准确 ，类似于and
- Should+Should：并集：结果最多，类似于Or
- Must+MustNot：补集：排除某结果

各种组合参考（自己理解）：

1）、MUST和MUST表示“与”的关系，即“交集”。

2）、MUST和MUST_NOT前者包含后者不包含。

3）、MUST_NOT和MUST_NOT没意义

4）、SHOULD与MUST表示MUST，SHOULD失去意义；

5）、SHOUlD与MUST_NOT相当于MUST与MUST_NOT。

6）、SHOULD与SHOULD表示“或”的概念。

## 7.2. 排序

排序、高亮、分页并不属于查询的条件，因此并不是在`query`内部，而是与`query`平级，

基本语法：

```
GET /{索引库名称}/_search
{
  "query": { ... },
  "sort": [
    {
      "{排序字段}": {
        "order": "{asc或desc}"
      }
    }
  ]
}
```

`sort` 可以让我们按照不同的字段进行排序，并且通过`order`指定排序的方式

**注意: 分词字段不能参与排序哦~~**

```
# 排序
GET /heima/_search
{
  "query": {
    "match": {
      "title": "手机"
    }
  },
  "sort": [
    {
      "price": {
        "order": "desc"
      }
    }
  ]
}
```

结果：

```
{
  "took" : 5,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 7,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : null,
        "_source" : {
          "title" : "三星 B9120 钛灰色 联通3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2899
        },
        "sort" : [
          2899.0
        ]
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : null,
        "_source" : {
          "title" : "三星 Note II (N7100) 云石白 联通3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2599
        },
        "sort" : [
          2599.0
        ]
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "9",
        "_score" : null,
        "_source" : {
          "title" : "华为 P6 (P6-C00) 黑 电信3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2340
        },
        "sort" : [
          2340.0
        ]
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "8",
        "_score" : null,
        "_source" : {
          "title" : "酷派 8076D 咖啡棕 移动3G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 1499
        },
        "sort" : [
          1499.0
        ]
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "4",
        "_score" : null,
        "_source" : {
          "title" : "中兴 U288 珠光白 移动3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 1099
        },
        "sort" : [
          1099.0
        ]
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "6",
        "_score" : null,
        "_source" : {
          "title" : "诺基亚(NOKIA) 1050 (RM-908) 黑色 移动联通2G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 998
        },
        "sort" : [
          998.0
        ]
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "5",
        "_score" : null,
        "_source" : {
          "title" : "飞利浦 老人手机 (X2560) 喜庆红 移动联通2G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 500
        },
        "sort" : [
          500.0
        ]
      }
    ]
  }
}
```

## 7.3. 高亮

高亮是在搜索结果中把搜索关键字标记出来，因此必须使用match这样的条件搜索。

elasticsearch中实现高亮的语法比较简单：

```
GET /heima/_search
{
  "query": {
    "match": {
      "title": "联通手机"
    }
  },
  "highlight": {
    "pre_tags": "<em>",
    "post_tags": "</em>", 
    "fields": {
      "title": {}
    }
  }
}
```

在使用match查询的同时，加上一个highlight属性：

- pre_tags：前置标签，可以省略，默认是em
- post_tags：后置标签，可以省略，默认是em
- fields：需要高亮的字段
  - title：这里声明title字段需要高亮，后面可以为这个字段设置特有配置，也可以空

结果：

```
{
  "took" : 6,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 7,
      "relation" : "eq"
    },
    "max_score" : 1.8434389,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "5",
        "_score" : 1.8434389,
        "_source" : {
          "title" : "飞利浦 老人手机 (X2560) 喜庆红 移动联通2G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 500
        },
        "highlight" : {
          "title" : [
            "飞利浦 老人<em>手机</em> (X2560) 喜庆红 移动<em>联通</em>2G<em>手机</em> 双卡双待"
          ]
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "6",
        "_score" : 1.761483,
        "_source" : {
          "title" : "诺基亚(NOKIA) 1050 (RM-908) 黑色 移动联通2G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 998
        },
        "highlight" : {
          "title" : [
            "诺基亚(NOKIA) 1050 (RM-908) 黑色 移动<em>联通</em>2G<em>手机</em>"
          ]
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "2",
        "_score" : 1.6723943,
        "_source" : {
          "title" : "三星 Note II (N7100) 云石白 联通3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2599
        },
        "highlight" : {
          "title" : [
            "三星 Note II (N7100) 云石白 <em>联通</em>3G<em>手机</em>"
          ]
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "1",
        "_score" : 1.6723943,
        "_source" : {
          "title" : "三星 B9120 钛灰色 联通3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2899
        },
        "highlight" : {
          "title" : [
            "三星 B9120 钛灰色 <em>联通</em>3G<em>手机</em> 双卡双待双通"
          ]
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "4",
        "_score" : 0.83515364,
        "_source" : {
          "title" : "中兴 U288 珠光白 移动3G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 1099
        },
        "highlight" : {
          "title" : [
            "中兴 U288 珠光白 移动3G<em>手机</em>"
          ]
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "8",
        "_score" : 0.8129092,
        "_source" : {
          "title" : "酷派 8076D 咖啡棕 移动3G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 1499
        },
        "highlight" : {
          "title" : [
            "酷派 8076D 咖啡棕 移动3G<em>手机</em> 双卡双待"
          ]
        }
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "9",
        "_score" : 0.73464036,
        "_source" : {
          "title" : "华为 P6 (P6-C00) 黑 电信3G手机 双卡双待双通",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 2340
        },
        "highlight" : {
          "title" : [
            "华为 P6 (P6-C00) 黑 电信3G<em>手机</em> 双卡双待双通"
          ]
        }
      }
    ]
  }
}

```

## 7.4. 分页

通过from和size来指定分页的开始位置及每页大小。

如果size不指定, 默认为10

语法：

```
GET /heima/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "price": {
        "order": "asc"
      }
    }
  ],
  "from": 0,
  "size": 2
}
```

结果：

```
{
  "took" : 0,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 10,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "5",
        "_score" : null,
        "_source" : {
          "title" : "飞利浦 老人手机 (X2560) 喜庆红 移动联通2G手机 双卡双待",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 500
        },
        "sort" : [
          500.0
        ]
      },
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "6",
        "_score" : null,
        "_source" : {
          "title" : "诺基亚(NOKIA) 1050 (RM-908) 黑色 移动联通2G手机",
          "images" : "http://image.leyou.com/12479122.jpg",
          "price" : 998
        },
        "sort" : [
          998.0
        ]
      }
    ]
  }
}
```

但是，其本质是逻辑分页，因此为了避免深度分页的问题，ES限制最多查到第10000条。

如果需要查询到10000以后的数据，你可以采用两种方式：

- scroll滚动查询
- search after

详见文档：https://www.elastic.co/guide/en/elasticsearch/reference/7.5/search-request-body.html#request-body-search-search-after



## 7.5. Filter过滤

当我们在京东这样的电商网站购物，往往查询条件不止一个，还会有很多过滤条件：

![image-20200104112102826](assets/image-20200104112102826.png)



而在默认情况下，所有的查询条件、过滤条件都会影响打分和排名。而对搜索结果打分是比较影响性能的，因此我们一般只对用户输入的搜索条件对应的字段打分，其它过滤项不打分。此时就不能简单实用布尔查询的must来组合条件了，而是实用`filter`方式。

示例，比如我们要查询`手机`，同时对价格过滤，原本的写法是这样的：

```
GET /heima/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "title": "手机"
          }
        },
        {
          "range": {
            "price": {
              "gte": 2099,
              "lte": 2999
            }
          }
        }
      ]
    }
  }
}
```

现在要修改成这样：

```
GET /heima/_search
{
  "query": {
    "bool": {
      "must": {
        "match": {
          "title": "手机"
        }
      },
      "filter": [
          {
            "range": {
                "price": {
                  "gte": 2099,
                  "lte": 2999
                }
             }
          }
      ]
    }
  }
}
```



## 7.6.`_source`筛选

默认情况下，elasticsearch在搜索的结果中，会把文档中保存在`_source`的所有字段都返回。

如果我们只想获取其中的部分字段，我们可以添加`_source`的过滤

### 7.6.1.直接指定字段

示例：

```
GET /heima/_search
{
  "_source": ["title","price"],
  "query": {
    "term": {
      "price": 2699
    }
  }
}
```

返回的结果：

```
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 1,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "heima",
        "_type" : "_doc",
        "_id" : "5",
        "_score" : 1.0,
        "_source" : {
          "price" : 500,
          "title" : "飞利浦 老人手机 (X2560) 喜庆红 移动联通2G手机 双卡双待"
        }
      }
    ]
  }
}
```

### 7.6.2.指定includes和excludes

我们也可以通过：

- includes：来指定想要显示的字段
- excludes：来指定不想要显示的字段

二者都是可选的。

示例：

```
GET /heima/_search
{
  "_source": {
    "includes":["title","price"]
  },
  "query": {
    "term": {
      "price": 500
    }
  }
}
```

与下面的结果将是一样的：

```
GET /heima/_search
{
  "_source": {
     "excludes": ["images"]
  },
  "query": {
    "term": {
      "price": 500
    }
  }
}
```

# 8.aggs聚合 

聚合（aggregations）可以让我们极其方便的实现对数据的统计、分析。例如：

- 什么品牌的手机最受欢迎？
- 这些手机的平均价格、最高价格、最低价格？
- 这些手机每月的销售情况如何？

实现这些统计功能的比数据库的sql要方便的多，而且查询速度非常快，可以实现近实时搜索效果。

要注意：**参与聚合的字段，必须是keyword类型 和 数值类型 **。



## 8.1 基本概念

Elasticsearch中的聚合，包含多种类型，最常用的两种，一个叫`桶`，一个叫`度量`：

> **桶（bucket）**

桶的作用，是按照某种方式对数据进行分组，每一组数据在ES中称为一个`桶`，例如我们根据国籍对人划分，可以得到`中国桶`、`英国桶`，`日本桶`……或者我们按照年龄段对人进行划分：0~10,10~20,20~30,30~40等。

Elasticsearch中提供的划分桶的方式有很多：

- Date Histogram Aggregation：根据日期阶梯分组，例如给定阶梯为周，会自动每周分为一组
- Histogram Aggregation：根据数值阶梯分组，与日期类似，需要知道分组的间隔（interval）
- Terms Aggregation：根据词条内容分组，词条内容完全匹配的为一组，类似数据库group by 
- Range Aggregation：数值和日期的范围分组，指定开始和结束，然后按段分组
- ……

综上所述，我们发现bucket aggregations 只负责对数据进行分组，并不进行计算，因此往往bucket中往往会嵌套另一种聚合：metrics aggregations即度量

> **度量（metrics）**  

分组完成以后，我们一般会对组中的数据进行聚合运算，例如求平均值、最大、最小、求和等，这些在ES中称为`度量`

比较常用的一些度量聚合方式：

- Avg Aggregation：求平均值
- Max Aggregation：求最大值
- Min Aggregation：求最小值
- Percentiles Aggregation：求百分比
- Stats Aggregation：同时返回avg、max、min、sum、count等
- Sum Aggregation：求和
- Top hits Aggregation：求前几
- Value Count Aggregation：求总数
- ……



为了测试聚合，我们先批量导入一些数据

创建索引：

```
PUT /car
{
  "mappings": {
    "properties": {
      "color": {
        "type": "keyword"
      },
      "make": {
        "type": "keyword"
      }
    }
  }
}
```

**注意**：在ES中，需要进行聚合、排序的字段其处理方式比较特殊，因此不能被分词，必须使用`keyword`或`数值类型`。这里我们将color和make这两个文字类型的字段设置为keyword类型，这个类型不会被分词，将来就可以参与聚合

导入数据，这里是采用批处理的API，大家直接复制到kibana运行即可：

```
POST /car/_bulk
{ "index": {}}
{ "price" : 10000, "color" : "红", "make" : "本田", "sold" : "2014-10-28" }
{ "index": {}}
{ "price" : 20000, "color" : "红", "make" : "本田", "sold" : "2014-11-05" }
{ "index": {}}
{ "price" : 30000, "color" : "绿", "make" : "福特", "sold" : "2014-05-18" }
{ "index": {}}
{ "price" : 15000, "color" : "蓝", "make" : "丰田", "sold" : "2014-07-02" }
{ "index": {}}
{ "price" : 12000, "color" : "绿", "make" : "丰田", "sold" : "2014-08-19" }
{ "index": {}}
{ "price" : 20000, "color" : "红", "make" : "本田", "sold" : "2014-11-05" }
{ "index": {}}
{ "price" : 80000, "color" : "红", "make" : "宝马", "sold" : "2014-01-01" }
{ "index": {}}
{ "price" : 25000, "color" : "蓝", "make" : "福特", "sold" : "2014-02-12" }
```

**度量计算**

类似数据库中聚合运算，es提供了更为丰富的各种运算分析方法

```
语法:
{
    "query":{},
    ...
    "aggs":{
        "自定义的聚合处理名称": {
            "聚合类型":{
                "field":"域字段"
            }
        }
    }
    
}

常用度量类型:
count   数量
min     最小值
max     最大值
avg     平均值
sum     求和
stats   显示上面5种信息

get /car/_search
{
  "query":{
    "match_all": {}
  },
  "aggs":{
    "avg_price":{
      "stats": {
        "field": "price"
      }
    }
  }
}
// 对价格进行分析  的结果
"aggregations" : {
    "avg_price" : {
      "count" : 8,    //8条记录
      "min" : 10000.0,  // 最小值
      "max" : 80000.0,  // 最大值
      "avg" : 26500.0,  // 平均值
      "sum" : 212000.0  // 所有价格之和
    }
  }

```

## 8.2 聚合为桶

类似于数据库中的分组，

首先，我们按照 汽车的颜色`color来`划分`桶`，按照颜色分桶，最好是使用TermAggregation类型，按照颜色的名称来分桶。

```
GET /car/_search
{
    "size" : 0,
    "aggs" : { 
        "my_popular_colors" : { 
            "terms" : { 
              "field" : "color"
            }
        }
    }
}
```

- size： 查询条数，这里设置为0，因为我们不关心搜索到的数据，只关心聚合结果，提高效率
- aggs：声明这是一个聚合查询，是aggregations的缩写
  - popular_colors：给这次聚合起一个名字，可任意指定。
    - terms：聚合的类型，这里选择terms，是根据词条内容（这里是颜色）划分
      - field：划分桶时依赖的字段

结果：

```
{
  "took": 33,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 8,
    "max_score": 0,
    "hits": []
  },
  "aggregations": {
    "my_popular_colors": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "红",
          "doc_count": 4
        },
        {
          "key": "绿",
          "doc_count": 2
        },
        {
          "key": "蓝",
          "doc_count": 2
        }
      ]
    }
  }
}
```

- hits：查询结果为空，因为我们设置了size为0
- aggregations：聚合的结果
- my_popular_colors：我们定义的聚合名称
- buckets：查找到的桶，每个不同的color字段值都会形成一个桶
  - key：这个桶对应的color字段的值
  - doc_count：这个桶中的文档数量

通过聚合的结果我们发现，目前红色的小车比较畅销！



## 8.3  桶内度量

前面的例子告诉我们每个桶里面的文档数量，这很有用。 但通常，我们的应用需要提供更复杂的文档度量。 例如，每种颜色汽车的平均价格是多少？

因此，我们需要告诉Elasticsearch`使用哪个字段`，`使用何种度量方式`进行运算，这些信息要嵌套在`桶`内，`度量`的运算会基于`桶`内的文档进行

现在，我们为刚刚的聚合结果添加 求价格平均值的度量：

```
GET /car/_search
{
    "size" : 0,
    "aggs" : { 
        "my_popular_colors" : { 
            "terms" : { 
              "field" : "color"
            },
            "aggs":{
                "my_avg_price": { 
                   "avg": {
                      "field": "price" 
                   }
                }
            }
        }
    }
}
```

- aggs：我们在上一个aggs(my_popular_colors)中添加新的aggs。可见度量也是一个聚合
- my_avg_price：聚合的名称
- avg：度量的类型，这里是求平均值
- field：度量运算的字段



结果：

```
{
  "took" : 8,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 8,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [ ]
  },
  "aggregations" : {
    "my_popular_colors" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "红",
          "doc_count" : 4,
          "my_avg_price" : {
            "value" : 32500.0
          }
        },
        {
          "key" : "绿",
          "doc_count" : 2,
          "my_avg_price" : {
            "value" : 21000.0
          }
        },
        {
          "key" : "蓝",
          "doc_count" : 2,
          "my_avg_price" : {
            "value" : 20000.0
          }
        }
      ]
    }
  }
}

```

可以看到每个桶中都有自己的`avg_price`字段，这是度量聚合的结果

## 8.4 桶内嵌套桶

刚刚的案例中，我们在桶内嵌套度量运算。事实上桶不仅可以嵌套运算， 还可以再嵌套其它桶。也就是说在每个分组中，再分更多组。

比如：我们想统计每种颜色的汽车中，分别属于哪个制造商，按照`make`字段再进行分桶

```
GET /car/_search
{
    "size" : 0,
    "aggs" : { 
        "my_popular_colors" : { 
            "terms" : { 
              "field" : "color"
            },
            "aggs":{
                "my_avg_price": { 
                   "avg": {
                      "field": "price" 
                   }
                },
                "my_maker":{
                    "terms":{
                        "field":"make"
                    }
                }
            }
        }
    }
}
```

- 原来的color桶和avg计算我们不变
- my_maker：在嵌套的aggs下新添一个桶，叫做my_maker
- terms：桶的划分类型依然是词条
- filed：这里根据make字段进行划分



部分结果：

```
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 8,
      "relation" : "eq"
    },
    "max_score" : null,
    "hits" : [ ]
  },
  "aggregations" : {
    "my_popular_colors" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "红",
          "doc_count" : 4,
          "my_avg_price" : {
            "value" : 32500.0
          },
          "my_maker" : {
            "doc_count_error_upper_bound" : 0,
            "sum_other_doc_count" : 0,
            "buckets" : [
              {
                "key" : "本田",
                "doc_count" : 3
              },
              {
                "key" : "宝马",
                "doc_count" : 1
              }
            ]
          }
        },
        {
          "key" : "绿",
          "doc_count" : 2,
          "my_avg_price" : {
            "value" : 21000.0
          },
          "my_maker" : {
            "doc_count_error_upper_bound" : 0,
            "sum_other_doc_count" : 0,
            "buckets" : [
              {
                "key" : "丰田",
                "doc_count" : 1
              },
              {
                "key" : "福特",
                "doc_count" : 1
              }
            ]
          }
        },
        {
          "key" : "蓝",
          "doc_count" : 2,
          "my_avg_price" : {
            "value" : 20000.0
          },
          "my_maker" : {
            "doc_count_error_upper_bound" : 0,
            "sum_other_doc_count" : 0,
            "buckets" : [
              {
                "key" : "丰田",
                "doc_count" : 1
              },
              {
                "key" : "福特",
                "doc_count" : 1
              }
            ]
          }
        }
      ]
    }
  }
}

```

- 我们可以看到，新的聚合`maker`被嵌套在原来每一个`color`的桶中。
- 每个颜色下面都根据 `make`字段进行了分组
- 我们能读取到的信息：
  - 红色车共有4辆
  - 红色车的平均售价是 $32，500 美元。
  - 其中3辆是 Honda 本田制造，1辆是 BMW 宝马制造。