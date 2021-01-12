# 分布式搜索引擎-Elasticsearch-2

课程目标：

- Java对ES的基本操作：索引库操作、文档的基本CRUD
- Java实现对ES的各种查询：各种规则查询、分页、过滤、高亮、聚合
- 了解es集群的概念

# 一、Java客户端对ES的基本操作

## 官方提供的client客户端

在elasticsearch官网中提供了各种语言的客户端：https://www.elastic.co/guide/en/elasticsearch/client/index.html

而Java的客户端就有两个：

![image-20200104164045946](assets/image-20200104164045946.png) 

不过Java API这个客户端（Transport Client）已经在7.0以后过期了，而且在8.0版本中将直接废弃。所以我们会学习Java REST Client：

![image-20200104164428873](assets/image-20200104164428873.png) 

然后再选择High Level REST Client这个。

Java  REST Client 其实就是利用Java语言向 ES服务发 Http的请求，因此请求和操作与前面学习的REST API 一模一样。

### 开发环境

新建基于Maven的Java项目，相关信息如下：

pom.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>cn.itcast.demo</groupId>
    <artifactId>esdemo</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <!--elastic客户端-->
        <!--elastic客户端-->
        <dependency>
         <groupId>org.elasticsearch.client</groupId>
         <artifactId>elasticsearch-rest-high-level-client</artifactId>
         <version>7.4.2</version>
        </dependency>
        <!-- Junit单元测试 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <!--lombok  @Data -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
        </dependency>
        <!--JSON工具 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.49</version>
        </dependency>
        <!--common工具-->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.8.1</version>
        </dependency>
    </dependencies>
</project>
```

实体类：

cn.itcast.esdemo.pojo.User

```java
package cn.itcast.esdemo.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;

@Data
@AllArgsConstructor
public class User {
    private Long id;
    private String name;// 姓名
    private Integer age;// 年龄
    private String gender;// 性别
    private String note;// 备注
}
```



扩展：

使用Lombok需要两个条件：

1）引入依赖：

```xml
<!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
        </dependency>
```

2）编辑器idea安装插件：

在线装，参考：https://plugins.jetbrains.com/plugin/6317-lombok

![image-20200220095157760](assets/image-20200220095157760.png)



## 连接ES

在官网上可以看到连接ES的教程：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high-getting-started-initialization.html

首先需要与ES建立连接，ES提供了一个客户端RestHighLevelClient。

代码如下：

```java
RestHighLevelClient client = new RestHighLevelClient(
        RestClient.builder(
                new HttpHost("localhost", 9200, "http"),
                new HttpHost("localhost", 9201, "http")));
```

ES中的所有操作都是通过RestHighLevelClient来完成的：

![image-20200105103815463](assets/image-20200105103815463.png) 



为了后面测试方便，我们写到一个单元测试中，并且通过`@Before`注解来初始化客户端连接。

cn.itcast.esdemo.ElasticSearchTest

```java
package cn.itcast.esdemo;

import org.apache.http.HttpHost;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.junit.After;
import org.junit.Before;

import java.io.IOException;
//ES测试类
public class ElasticSearchTest {
    //客户端对象
    private RestHighLevelClient client;
    /**
     * 建立连接
     */
    @Before
    public void init() throws IOException {
        //创建Rest客户端
        client = new RestHighLevelClient(
                RestClient.builder(
                        //如果是集群，则设置多个主机，注意端口是http协议的端口
                        new HttpHost("localhost", 9200, "http")
//                        ,new HttpHost("localhost", 9201, "http")
//                        ,new HttpHost("localhost", 9202, "http")
                )
        );
    }

    /**
     * 关闭客户端连接
     */
    @After
    public void close() throws IOException {
        client.close();
    }
}

```

## 建库和映射

开发中，往往库和映射的操作一起完成，官网详细文档地址：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.x/_index_apis.html



这里我们主要实现库和映射的创建。查询、删除等功能大家可参考文档自己实现。

![image-20200105093038617](assets/image-20200105093038617.png)



### 思路分析

按照官网给出的步骤，创建索引包括下面几个步骤：

- 1）创建CreateIndexRequest对象，并指定索引库名称
- 2）指定settings配置
- 3）指定mapping配置
- 4）发起请求，得到响应

其实仔细分析，与我们在Kibana中的Rest风格API完全一致：

```json
PUT /heima
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 1
  },
  "mappings": {
    
  }
}
```



### 设计映射规则

Java代码中设置mapping，依然与REST中一致，需要JSON风格的映射规则。因此我们先在kibana中给User实体类定义好映射规则。

User包括下面的字段：

- Id：主键，在ES中是唯一标示，数字，可以选择long类型
- name：姓名，字符串类型，但是无需分词，使用keyword
- age：年龄，整数，可以使用integer
- gender：性别，字符串类型，但是无需分词，使用keyword
- note：备注，用户详细信息，字符串类型。需要分词，使用text

映射如下：

```json
PUT /user
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "id": {
        "type": "long"
      },
      "name":{
        "type": "keyword"
      },
      "age":{
        "type": "integer"
      },
      "gender":{
        "type": "keyword"
      },
      "note":{
        "type": "text",
        "analyzer": "ik_max_word"
      }
    }
  }
}
```

### 代码实现

我们在上面新建的ElasticDemo类中新建单元测试，完成代码，思路就是之前分析的4步骤：

- 1）创建CreateIndexRequest对象，并指定索引库名称
- 2）指定settings配置
- 3）指定mapping配置
- 4）发起请求，得到响应

```java
private RestHighLevelClient client;

    /**
     * 创建索引
     * @throws IOException
     */
    @Test
    public void testCreateIndex() throws IOException {
        // 1.创建CreateIndexRequest对象，并指定索引库名称
        CreateIndexRequest request = new CreateIndexRequest("user");
        // 2.指定settings配置(可以默认)
        request.settings(Settings.builder()
                .put("index.number_of_shards", 3)
                .put("index.number_of_replicas", 1)
        );
        // 3.指定mapping配置
        request.mapping(
                "{\n" +
                        "    \"properties\": {\n" +
                        "      \"id\": {\n" +
                        "        \"type\": \"long\"\n" +
                        "      },\n" +
                        "      \"name\":{\n" +
                        "        \"type\": \"keyword\"\n" +
                        "      },\n" +
                        "      \"age\":{\n" +
                        "        \"type\": \"integer\"\n" +
                        "      },\n" +
                        "      \"gender\":{\n" +
                        "        \"type\": \"keyword\"\n" +
                        "      },\n" +
                        "      \"note\":{\n" +
                        "        \"type\": \"text\",\n" +
                        "        \"analyzer\": \"ik_max_word\"\n" +
                        "      }\n" +
                        "    }\n" +
                        "  }",
                //指定映射的内容的类型为json
                XContentType.JSON);
        // 4.发起请求，得到响应（同步操作）
        CreateIndexResponse response = client.indices()
                .create(request, RequestOptions.DEFAULT);

        //打印结果
        System.out.println("response = " + response.isAcknowledged());
    }
```

返回结果：

```
response = true
```

## 文档增加

文档操作包括：新增文档、查询文档、修改文档、删除文档等。

CRUD官网地址：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.x/java-rest-high-supported-apis.html

新增的官网地址：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.x/java-rest-high-document-index.html

### 实现思路

根据官网文档，实现的步骤如下：

- 1）创建IndexRequest对象，并指定索引库名称
- 2）指定新增的数据的id
- 3）将新增的文档数据变成JSON格式
- 4）将JSON数据添加到IndexRequest中
- 5）发起请求，得到结果

### 具体代码

新增文档：

```java
   /**
     * 测试插入一个文档
     * @throws IOException
     */
    @Test
    public void testAddDocument() throws IOException {
        // 1.准备文档数据
        User user = new User(1L,"Rose",18,"0","Rose同学在学Java");
        // 2.创建IndexRequest对象，并指定索引库名称
        IndexRequest request = new IndexRequest("user");
        // 3.指定新增的数据的id，这里的id一定要用string,如果不指定id，则id为随机
        request.id(user.getId().toString());
        // 4.将新增的文档数据变成JSON格式
        String jsonStringData = JSON.toJSONString(user);
        // 5.将JSON数据添加到IndexRequest中
        request.source(jsonStringData, XContentType.JSON);
        // 6.发起请求，得到结果
        IndexResponse indexResponse = client.index(request, RequestOptions.DEFAULT);

        System.out.println("indexResponse = " + indexResponse.getResult());
    }
```

结果：

```
indexResponse = CREATED
```

### 新增的ID一致时

我们直接测试过，新增的时候如果ID存在则变成修改，我们试试，再次执行刚才的代码，可以看到结果变了：

```
indexResponse = UPDATED
```

## 根据id查询

官网地址：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.x/java-rest-high-document-get.html

### 实现思路

这里的查询是根据id查询，必须知道文档的id才可以。

根据官网文档，实现的步骤如下：

- 1）创建GetRequest 对象，并指定索引库名称、文档ID
- 2）发起请求，得到结果
- 3）从结果中得到source，是json字符串
- 4）将JSON反序列化为对象



### 具体代码

```java
    /**
     * 测试根据id查询一个文档
     * @throws IOException
     */
    @Test
    public void testfindDocumentById() throws IOException {
        // 1.创建GetRequest对象，并指定索引库名称、文档ID
        GetRequest request = new GetRequest("user", "1");
        // 2.发起请求，得到结果
        GetResponse response = client.get(request, RequestOptions.DEFAULT);
        // 3.从结果中得到source，是json字符串
        String source = response.getSourceAsString();
        System.out.println("source="+source);
        // 4.将JSON反序列化为对象
        User user = JSON.parseObject(source, User.class);
        System.out.println("user = " + user);
    }
}
```

结果如下：

```
source={"age":18,"gender":"0","id":1,"name":"Rose","note":"Rose同学在学Java"}
user = User(id=1, name=Rose, age=18, gender=0, note=Rose同学在学Java)
```



## 文档修改

新增时，如果ID一致就会覆盖旧的数据，实现修改。不过，如果我们只修改文档中的某个字段，可以使用另外的API：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.x/java-rest-high-document-update.html

### 思路

根据官网信息，修改时需要指定某个已经存在的文档的id、然后指定要修改的字段及新的值。

基本步骤如下：

- 1.创建UpdateRequest对象，指定索引库名称、文档ID
- 2.指定要修改的字段及属性值
- 3.发起请求

### 代码实现

```java
    /**
     * 测试根据id修改文档
     * @throws IOException
     */
    @Test
    public void testUpdateDocumentById() throws IOException {
        // 1.创建UpdateRequest对象，指定索引库名称、文档ID
        UpdateRequest request = new UpdateRequest(
                "user",
                "1");
        // 2.指定要修改的字段及属性值
        request.doc("gender", "1");
        // 3.发起请求
        UpdateResponse updateResponse = client.update(
                request, RequestOptions.DEFAULT);

        System.out.println("updateResponse = " + updateResponse.getResult());
    }
```

结果如下：

```java
updateResponse = UPDATED
```

如果再次查询，可以发现Rose已经成功变性了：

```
source={"age":18,"gender":"1","id":1,"name":"Rose","note":"Rose同学在学Java"}
user = User(id=1, name=Rose, age=18, gender=1, note=Rose同学在学Java)
```



## 文档删除

官网地址：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.x/java-rest-high-document-delete.html

实现思路非常简单，直接根据ID删除即可：

- 1.创建DeleteRequest对象，指定索引库名称、文档ID
- 2.发起请求

代码实现：

```java
    /**
     * 根据id删除文档
     * @throws IOException
     */
    @Test
    public void testDeleteDocumentById() throws IOException {
        // 1.创建DeleteRequest对象，指定索引库名称、文档ID
        DeleteRequest request = new DeleteRequest(
                "user",
                "1");
        // 2.发起请求
        DeleteResponse deleteResponse = client.delete(
                request, RequestOptions.DEFAULT);

        System.out.println("deleteResponse = " + deleteResponse.getResult());
    }
```

结果：

```
deleteResponse = DELETED
```



## 批量处理

如果我们需要把数据库中的所有用户信息都导入索引库，可以批量查询出多个用户，但是刚刚的新增文档是一次新增一个文档，这样效率太低了。

因此ElasticSearch提供了批处理的方案：BulkRequest

https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.x/java-rest-high-document-bulk.html

### 思路分析

> A `BulkRequest` can be used to execute multiple index, update and/or delete operations using a single request.

一个BulkRequest可以在一次请求中执行多个 新增、更新、删除请求。

所以，BulkRequest就是把多个其它增、删、改请求整合，然后一起发送到ES来执行。

我们拿批量新增来举例，步骤如下：

- 1.从数据库查询文档数据

- 2.创建BulkRequest对象
- 3.创建多个IndexRequest对象，组织文档数据，并添加到BulkRequest中
- 4.发起请求

### 具体代码

```java
    /**
     * 大量数据批量添加
     * @throws IOException
     */
    @Test
    public void testBulkAddDocumentList() throws IOException {
        // 1.从数据库查询文档数据
        //第一步：准备数据源。本案例使用List来模拟数据源。
        List<User> users = Arrays.asList(
      new User(1L, "Rose", 18, "1", "Rose同学在传智播客学表演"),
      new User(2L, "Jack", 38, "1", "Jack同学在黑马学JavaEE"),
      new User(3L, "小红", 23, "0", "小红同学在传智播客学唱歌"),
      new User(4L, "小明", 20, "1", "小明同学在黑马学JavaSE"),
      new User(5L, "达摩", 33, "1", "达摩和尚在达摩院学唱歌"),
      new User(6L, "鲁班", 24, "1", "鲁班同学走在乡间小路上"),
      new User(7L, "孙尚香", 26, "0", "孙尚香同学想带阿斗回东吴"),
      new User(8L, "李白", 27, "1", "李白同学在山顶喝着酒唱着歌"),
      new User(9L, "甄姬", 28, "0", "甄姬同学弹奏一曲东风破"),
      new User(10L, "虞姬", 27, "0", "虞姬同学在和项羽谈情说爱")
        );
        // 2.创建BulkRequest对象
        BulkRequest bulkRequest = new BulkRequest();
        // 3.创建多个IndexRequest对象，并添加到BulkRequest中
        for (User user : userList) {
            bulkRequest.add(new IndexRequest("user")
                    .id(user.getId().toString())
                    .source(JSON.toJSONString(user), XContentType.JSON)
            );
        }
        // 4.发起请求
        BulkResponse bulkResponse = client.bulk(bulkRequest, RequestOptions.DEFAULT);

        System.out.println("status: " + bulkResponse.status());
    }
```

结果如下：

```
status: OK
```



可以再Kibana中通过`GET /user/_search`看到查询的结果。

提示：

可以批量处理增删改：

![image-20200220105716117](assets/image-20200220105716117.png)





##  实现各种查询

查询、搜索相关功能主要包括：

- 基本查询
  - 分词查询
  - 词条查询
  - 范围查询
  - 布尔查询
    - Filter功能
- source筛选
- 排序
- 分页
- 高亮
- 聚合

官方文档：https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.x/java-rest-high-search.html



## 查询条件的核心API分析说明

先来回顾一下REST风格中查询的语法：

 

整个请求对象是一个大JSON对象，包含5部分属性：

- query：查询属性

- sort：排序属性

- from和size：分页属性

- highlight：高亮属性

  ```
  GET /索引库名称/_search 
  	{
  		"query":{ // 查询条件
  			"bool":{ // 布尔查询
  				"must":[ //里面的多个条件必须全部满足
  					{
  						"match_all":{}   //查询全部 
  					},
  					{
  						"term":{"域字段":"字段值"}  //关键词查询 
  					},
  					{
  						"match":{"域字段":"字段值"} //分词查询 
  					},
  					{
  						"range":{ //范围查询 
  							"字段":{
  								"lt":"值",
  								"gt":"值"
  							}
  						}
  					},
  					{
  					}
  				],  
  				"must_not":[], //里面的多个条件必须全部不满足
  				"should":[],//里面的多个条件 满足一个即可
  				"filter":[] //根据条件过滤数据 (不计算得分，效率高)
  			}
  		},
  		sort:[
  			{
  				"排序字段":{
  					"order":"asc或desc"
  				}
  			}
  		],
  		from:0,  // 从第几条记录查  
  		size:2,   // 查询多少条
  		"highlight":{ 
  			"pre_tags": "<em>",   // 高亮的前置标签
  			"post_tags": "</em>", // 高亮的后置标签
  			"fields": {      // 高亮字段 
  				"title": {}
  			}
  		},
  		"aggs":{
              "聚合自定义名称":{
                  "聚合类型":{
                      "field":"具体的字段"
                  }
              }
  		}
  	}
  ```


而Java客户端，其实也是在构建这样的JSON对象。



### SearchSourceBuilder

在Java客户端中，`SearchSourceBuilder`就是用来构建上面提到的大JSON对象，其中包含了5个方法：

- query(QueryBuilder)：查询条件
- sort(String, SortOrder)：排序条件
- from(int)和size(int)：分页条件
- highlight(HighlightBuilder)：高亮条件
- aggregation(AggregationBuilder)：聚合条件

如图：

![image-20200105154343366](assets/image-20200105154343366-1581587688758.png) 

是不是与REST风格API的JSON对象一致？

接下来，再逐个来看每一个查询子属性。

### 查询条件QueryBuilders

SearchSourceBuilder的query(QueryBuilder)方法，用来构建查询条件，而查询分为：

- 分词查询：MatchQuery
- 词条查询：TermQuery
- 布尔查询：BooleanQuery
- 范围查询：RangeQuery
- 模糊查询：FuzzyQuery
- ...

这些查询有一个统一的工具类来提供：QueryBuilders

![image-20200105155220039](assets/image-20200105155220039-1581587688758.png) 



## 搜索结果API分析说明

在Kibana中回顾看一下搜索结果：

![image-20200105163216526](assets/image-20200105163216526.png) 

搜索得到的结果整体是一个JSON对象，包含下列2个属性：

- hits：查询结果，其中又包含两个属性：
  - total：总命中数量
  - hits：查询到的文档数据，是一个数组，数组中的每个对象就包含一个文档结果，又包含：
    - _source：文档原始信息
    - highlight：高亮结果信息
- aggregations：聚合结果对象，其中包含多个属性，属性名称由添加聚合时的名称来确定：
  - gender_agg：这个是我们创建聚合时用的`聚合名称`，其中包含聚合结果
    - buckets：聚合结果数组

Java客户端中的SearchResponse代表整个JSON结果

### SearchResponse

Java客户端中的SearchResponse代表整个JSON结果，包含下面的方法：

![image-20200105164513323](assets/image-20200105164513323.png) 

包含两个方法：

- getHits()：返回的是SearchHits，代表查询结果
- getAggregations()：返回的是Aggregations，代表聚合结果



### SearchHits查询结果

SearchHits代表查询结果的JSON对象：

![image-20200105171202561](assets/image-20200105171202561.png) 



包含下面的方法：

![image-20200105165201949](assets/image-20200105165201949.png) 

核心方法有3个：

- getTotalHists()：返回TotalHists，总命中数
- getHits()：返回SearchHit数组
- getMaxScore()：返回float，文档的最大得分



### SearchHit结果对象

SearchHit封装的就是结果数组中的每一个JSON对象：

![image-20200105171414852](assets/image-20200105171414852.png) 

包含这样的方法：

![image-20200105171625893](assets/image-20200105171625893.png) 

- getSourceAsString()：返回的是`_source`
- getHighLightFields()：返回是高亮结果



## 基本查询

### 思路分析

步骤如下：

- 1.创建SearchSourceBuilder对象
  - 1.1.添加查询条件QueryBuilders
  - 1.2.添加排序、分页等其它条件
- 2.创建SearchRequest对象，并制定索引库名称
- 3.添加SearchSourceBuilder对象到SearchRequest对象中
- 4.发起请求，得到结果
- 5.解析结果SearchResponse
  - 5.1.获取总条数
  - 5.2.获取SearchHits数组，并遍历
    - 获取其中的`_source`，是JSON数据
    - 把`_source`反序列化为User对象

### 查询所有

QueryBuilders可以实现各种查询，比如查询所有：match_all

代码如下：

```java
    /**
     * 查询所有
     * @throws IOException
     */
    @Test
    public void testBasicSearch() throws IOException {
        // 1.创建SearchSourceBuilder对象
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //  1.1.添加查询条件QueryBuilders，这里选择match_all，查询所有
        sourceBuilder.query(
                QueryBuilders.matchAllQuery()
        );
        //  1.2.添加排序、分页等其它条件(暂忽略)

        // 2.创建SearchRequest对象，并指定索引库名称
        SearchRequest request = new SearchRequest("user");
        // 3.添加SearchSourceBuilder对象到SearchRequest对象中
        request.source(sourceBuilder);
        // 4.发起请求，得到结果
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 5.解析结果
        SearchHits searchHits = response.getHits();
        //  5.1.获取总条数
        long total = searchHits.getTotalHits().value;
        System.out.println("total = " + total);
        //  5.2.获取SearchHit数组，并遍历
        SearchHit[] hits = searchHits.getHits();
        for (SearchHit hit : hits) {
            //获取分数
            System.out.println("文档得分："+hit.getScore());
            //  - 获取其中的`_source`，是JSON数据
            String json = hit.getSourceAsString();
            //  - 把`_source`反序列化为User对象
            User user = JSON.parseObject(json, User.class);
            System.out.println("user = " + user);
        }
    }
```

结果如下：

```
total = 4
文档得分：1.0
user = User(id=1, name=Rose, age=18, gender=0, note=Rose同学在传智播客学表演)
文档得分：1.0
user = User(id=2, name=Jack, age=38, gender=1, note=Jack同学在黑马学JavaEE)
文档得分：1.0
user = User(id=3, name=小红, age=23, gender=0, note=小红同学在传智播客学唱歌)
文档得分：1.0
user = User(id=4, name=小明, age=20, gender=1, note=小明同学在黑马学JavaSE)
```



### 分词查询

MatchQuery就是分词查询，会对搜索的内容分词后查询：

```java
sourceBuilder.query(QueryBuilders.matchQuery("note", "唱歌表演"));
```

完整代码如下：

```java
    /**
     * 查询所有
     * @throws IOException
     */
    @Test
    public void testBasicSearch() throws IOException {
        // 1.创建SearchSourceBuilder对象
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //  1.1.添加查询条件QueryBuilders，这里选择match_all，查询所有
        sourceBuilder.query(
                //1）查询所有
//                QueryBuilders.matchAllQuery()
                //2）分词搜索
                QueryBuilders.matchQuery("note", "唱歌表演")
                
        );
        //  1.2.添加排序、分页等其它条件(暂忽略)

        // 2.创建SearchRequest对象，并制定索引库名称
        SearchRequest request = new SearchRequest("user");
        // 3.添加SearchSourceBuilder对象到SearchRequest对象中
        request.source(sourceBuilder);
        // 4.发起请求，得到结果
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 5.解析结果
        SearchHits searchHits = response.getHits();
        //  5.1.获取总条数
        long total = searchHits.getTotalHits().value;
        System.out.println("total = " + total);
        //  5.2.获取SearchHits数组，并遍历
        SearchHit[] hits = searchHits.getHits();
        for (SearchHit hit : hits) {
            //  - 获取其中的`_source`，是JSON数据
            String json = hit.getSourceAsString();
            //  - 把`_source`反序列化为User对象
            User user = JSON.parseObject(json, User.class);
            System.out.println("user = " + user);
        }
    }
```

结果：

```
total = 2
user = User(id=3, name=小红, age=23, gender=0, note=小红同学在传智播客学唱歌)
user = User(id=1, name=Rose, age=18, gender=0, note=Rose同学在传智播客学表演)

```



### 布尔查询

BooleanQuery就是布尔查询，需要把其它几个查询用must、must_not组合，。比如：

```java
// 布尔查询
BoolQueryBuilder queryBuilder = QueryBuilders.boolQuery();
// 添加must条件
queryBuilder.must(QueryBuilders.matchQuery("note", "唱歌表演"));
// 添加must条件
queryBuilder.must(QueryBuilders.rangeQuery("age").gte(18).lte(24));
sourceBuilder.query(queryBuilder);
```

完整代码：

```java
   /**
     * 查询所有
     * @throws IOException
     */
    @Test
    public void testBasicSearch() throws IOException {
        // 1.创建SearchSourceBuilder对象
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //  1.1.添加查询条件QueryBuilders，这里选择match_all，查询所有
//        sourceBuilder.query(
//                //1）查询所有
////                QueryBuilders.matchAllQuery()
//                //2）分词搜索
//                QueryBuilders.matchQuery("note", "唱歌表演")
//
//        );

        //构建查询规则QueryBuilder
        //布尔查询（组合条件查询）
        BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
        //组合各种条件：交集（mustmust）、并集(should should)、补集(must mustnot)
        //添加must条件
        boolQueryBuilder.must(QueryBuilders.matchQuery("note","唱歌表演"));
        //添加must条件
        boolQueryBuilder.must(QueryBuilders.rangeQuery("age").gte(18).lte(24));

        //添加查询规则
        sourceBuilder.query(boolQueryBuilder);

        //  1.2.添加排序、分页等其它条件(暂忽略)

        // 2.创建SearchRequest对象，并制定索引库名称
        SearchRequest request = new SearchRequest("user");
        // 3.添加SearchSourceBuilder对象到SearchRequest对象中
        request.source(sourceBuilder);
        // 4.发起请求，得到结果
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 5.解析结果
        SearchHits searchHits = response.getHits();
        //  5.1.获取总条数
        long total = searchHits.getTotalHits().value;
        System.out.println("total = " + total);
        //  5.2.获取SearchHits数组，并遍历
        SearchHit[] hits = searchHits.getHits();
        for (SearchHit hit : hits) {
            //  - 获取其中的`_source`，是JSON数据
            String json = hit.getSourceAsString();
            //  - 把`_source`反序列化为User对象
            User user = JSON.parseObject(json, User.class);
            System.out.println("user = " + user);
        }
    }

```



结果：

```
total = 1
user = User(id=1, name=Rose, age=18, gender=0, note=Rose同学在传智播客学表演)
```



## filter过滤

过滤条件最好使用filter来实现，不会参与打分。过滤的api是在布尔查询中的。

```java
// 布尔查询
BoolQueryBuilder queryBuilder = QueryBuilders.boolQuery();
// 添加must条件
queryBuilder.must(QueryBuilders.matchQuery("note", "唱歌表演"));
// 添加filter条件，不参与打分
queryBuilder.filter(QueryBuilders.rangeQuery("age").gte(18).lte(24));
sourceBuilder.query(queryBuilder);
```

完整代码：

```java
   /**
     * 查询所有
     * @throws IOException
     */
    @Test
    public void testBasicSearch() throws IOException {
        // 1.创建SearchSourceBuilder对象
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //  1.1.添加查询条件QueryBuilders，这里选择match_all，查询所有
//        sourceBuilder.query(
//                //1）查询所有
////                QueryBuilders.matchAllQuery()
//                //2）分词搜索
//                QueryBuilders.matchQuery("note", "唱歌表演")
//
//        );

        // 布尔查询
        BoolQueryBuilder queryBuilder = QueryBuilders.boolQuery();
        // 添加must条件
        queryBuilder.must(QueryBuilders.matchQuery("note", "唱歌表演"));
        // 添加filter条件，不参与打分
//        queryBuilder.must(QueryBuilders.rangeQuery("age").gte(18).lte(22));
        queryBuilder.filter(QueryBuilders.rangeQuery("age").gte(18).lte(22));
        sourceBuilder.query(queryBuilder);

        //  1.2.添加排序、分页等其它条件(暂忽略)

        // 2.创建SearchRequest对象，并制定索引库名称
        SearchRequest request = new SearchRequest("user");
        // 3.添加SearchSourceBuilder对象到SearchRequest对象中
        request.source(sourceBuilder);
        // 4.发起请求，得到结果
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 5.解析结果
        SearchHits searchHits = response.getHits();
        //  5.1.获取总条数
        long total = searchHits.getTotalHits().value;
        System.out.println("total = " + total);
        //  5.2.获取SearchHits数组，并遍历
        SearchHit[] hits = searchHits.getHits();
        for (SearchHit hit : hits) {
            //  - 获取其中的`_source`，是JSON数据
            String json = hit.getSourceAsString();
            //  - 把`_source`反序列化为User对象
            User user = JSON.parseObject(json, User.class);
            System.out.println("user = " + user);
        }
    }

```

## 排序

### API介绍

通过SearchSourceBuilder的sort(String, SortOrder)方法用来实现排序条件的封装：

```java
/**
  * Adds a sort against the given field name and the sort ordering.
  *
  * @param name The name of the field，排序字段名称
  * @param order The sort ordering，排序的方式
  */
public SearchSourceBuilder sort(String name, SortOrder order) {
    // ...
}
```

其中的SortOrder是一个枚举，包含ASC和DESC两个枚举项：

```java
public enum SortOrder implements Writeable {
    /**
     * Ascending order.
     */
    ASC {
		// ..
    },
    /**
     * Descending order.
     */
    DESC {
		// ..
    };
    // ...
}
```



### 具体实现

目标需求：要根据id正序排序



在原由查询的基础上，给SearchSourceBuilder中添加sort即可:

```java
//  1.2.添加排序条件
        //默认正序
//        sourceBuilder.sort("id");
        //倒序排序
        sourceBuilder.sort("id", SortOrder.DESC);
```

完整代码如下：

```java
  @Test
    public void testBasicSearchAndSort() throws IOException {
        // 1.创建SearchSourceBuilder对象
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //  1.1.添加查询条件QueryBuilders，这里选择match_all，查询所有
        sourceBuilder.query(QueryBuilders.matchAllQuery());

        //  1.2.添加排序条件
        //默认正序
//        sourceBuilder.sort("id");
        //倒序排序
        sourceBuilder.sort("id", SortOrder.DESC);

        // 2.创建SearchRequest对象，并制定索引库名称
        SearchRequest request = new SearchRequest("user");
        // 3.添加SearchSourceBuilder对象到SearchRequest对象中
        request.source(sourceBuilder);
        // 4.发起请求，得到结果
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 5.解析结果
        SearchHits searchHits = response.getHits();
        //  5.1.获取总条数
        long total = searchHits.getTotalHits().value;
        System.out.println("total = " + total);
        //  5.2.获取SearchHits数组，并遍历
        SearchHit[] searchHit = searchHits.getHits();
        for (SearchHit hit : searchHit) {
            //获取分数
            System.out.println("文档得分："+hit.getScore());
            //  - 获取其中的`_source`，是JSON数据
            String source = hit.getSourceAsString();
            //将json反序列化为java对象
            User user = JSON.parseObject(source, User.class);
            System.out.println("user = " + user);
        }
    }

```



查询结果：

```java
total = 4
user = User(id=1, name=Rose, age=18, gender=0, note=Rose同学在传智播客学表演)
user = User(id=2, name=Jack, age=38, gender=1, note=Rose同学在黑马学JavaEE)
user = User(id=3, name=小红, age=23, gender=0, note=小红同学在传智播客学唱歌)
user = User(id=4, name=小明, age=20, gender=1, note=Rose同学在黑马学JavaSE)
```



## 分页

在原由查询的基础上，给SearchSourceBuilder中添加from和size即可。例如我们的分页信息是：

page = 1，size = 5，代表查询第一页，每页5条，可以计算出： from = (page - 1) * size = 0

所以，代码如下：

```java
		// 1.3.添加分页条件
        int page = 2, pageSize = 2;
        //起始索引
        int from = (page - 1) * pageSize;
        //每页显示的最大记录数
        int size=pageSize;
        //设置起始索引，默认是0
        sourceBuilder.from(from);
        //设置最大记录数，默认20
        sourceBuilder.size(size);
```

完整代码：

```java
@Test
    public void testBasicSearchWithSortAndPage() throws IOException {
        // 1.创建SearchSourceBuilder对象
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //  1.1.添加查询条件QueryBuilders，这里选择match_all，查询所有
        sourceBuilder.query(QueryBuilders.matchAllQuery());
        //  1.2.添加排序、分页等其它条件
        sourceBuilder.sort("id", SortOrder.ASC);
        
        // 1.3.添加分页条件
        int page = 2, pageSize = 2;
        //起始索引
        int from = (page - 1) * pageSize;
        //每页显示的最大记录数
        int size=pageSize;
        //设置起始索引，默认是0
        sourceBuilder.from(from);
        //设置最大记录数，默认20
        sourceBuilder.size(size);


        // 2.创建SearchRequest对象，并制定索引库名称
        SearchRequest request = new SearchRequest("user");
        // 3.添加SearchSourceBuilder对象到SearchRequest对象中
        request.source(sourceBuilder);
        // 4.发起请求，得到结果
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 5.解析结果
        SearchHits searchHits = response.getHits();
        //  5.1.获取总条数
        long total = searchHits.getTotalHits().value;
        System.out.println("total = " + total);
        //  5.2.获取SearchHits数组，并遍历
        SearchHit[] searchHit = searchHits.getHits();
        for (SearchHit hit : searchHit) {
            //获取分数
            System.out.println("文档得分："+hit.getScore());
            //  - 获取其中的`_source`，是JSON数据
            String source = hit.getSourceAsString();
            //将json反序列化为java对象
            User user = JSON.parseObject(source, User.class);
            System.out.println("user = " + user);
        }
    }
```

结果如下：

```java
total = 4
user = User(id=3, name=小红, age=23, gender=0, note=小红同学在传智播客学唱歌)
user = User(id=4, name=小明, age=20, gender=1, note=Rose同学在黑马学JavaSE)
```



## 高亮

### 开启高亮

高亮需要在SearchSourceBuilder的highlighter()方法来实现：

```java
// 2.高亮，指定高亮字段这里是note字段
    sourceBuilder.highlighter(new HighlightBuilder().field("note"));
```

完整代码:

```java
@Test
public void testHighlight() throws IOException {
    // 1.创建SearchSourceBuilder对象
    SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
    //  1.1.添加查询条件QueryBuilders，
    sourceBuilder.query(QueryBuilders.matchQuery("note", "唱歌表演"));

    // 2.高亮，指定高亮字段这里是note字段
    sourceBuilder.highlighter(new HighlightBuilder().field("note"));

    // 3.创建SearchRequest对象，并制定索引库名称
    SearchRequest request = new SearchRequest("user");
    // 4.添加SearchSourceBuilder对象到SearchRequest对象中
    request.source(sourceBuilder);
    // 5.发起请求，得到结果
    SearchResponse response = client.search(request, RequestOptions.DEFAULT);
    // 6.解析结果
    SearchHits searchHits = response.getHits();
    //  6.1.获取总条数
    long total = searchHits.getTotalHits().value;
    System.out.println("total = " + total);
    //  6.2.获取SearchHits数组，并遍历
    SearchHit[] hits = searchHits.getHits();
    for (SearchHit hit : hits) {
        //  - 获取其中的`_source`，是JSON数据
        String json = hit.getSourceAsString();
        //  - 把`_source`反序列化为User对象
        User user = JSON.parseObject(json, User.class);
        System.out.println("user = " + user);
    }
}
```

运行，查看结果：

```
total = 2
user = User(id=3, name=小红, age=23, gender=0, note=小红同学在传智播客学唱歌)
user = User(id=1, name=Rose, age=18, gender=0, note=Rose同学在传智播客学表演)
```



结果并未高亮，为什么？

这是因为查询结果中，文档数据和高亮数据是分离的：

![image-20200105184133923](assets/image-20200105184133923.png) 

我们需要自己在搜索结果中解析高亮结果

### 解析高亮结果

搜索结果SearchHit对象中，包含两个方法：

- getSourceAsString()：返回的是`_source`
- getHighLightFields()：返回是高亮结果，Map<String, HighlightField>，map的key是高亮字段名称

解析SearchHit并高亮的代码片段如下：

```java
//  5.2.获取SearchHits数组，并遍历
        SearchHit[] searchHit = searchHits.getHits();
        for (SearchHit hit : searchHit) {
            //获取分数
            System.out.println("文档得分："+hit.getScore());
            //  - 获取其中的`_source`，是JSON数据
            String source = hit.getSourceAsString();
            //将json反序列化为java对象
            User user = JSON.parseObject(source, User.class);
            System.out.println("没有高亮的user = " + user);

            //获取高亮结果
            //获取当前文档的所有高亮了的字段结果，如果没有被高亮的，则不再这个集合中
            Map<String, HighlightField> highlightFields = hit.getHighlightFields();
            // 根据字段名，获取高亮字段（只能取到高亮了的字段）
            HighlightField highlightField = highlightFields.get("note");
            //获取到高亮片段（段落）数组
            Text[] fragments = highlightField.getFragments();
            //将高亮片段拼接到一起
            String noteHighlight = StringUtils.join(fragments);
            // 覆盖旧值(即使用高亮的值覆盖没高亮的值)
            user.setNote(noteHighlight);
            System.out.println("有高亮的user = " + user);
        }
```



运行结果：

```
total = 2
user = User(id=3, name=小红, age=23, gender=0, note=小红同学在传智播客学<em>唱歌</em>)
user = User(id=1, name=Rose, age=18, gender=0, note=Rose同学在传智播客学<em>表演</em>)
```



完整代码：

```java

    @Test
    public void testHighlight() throws IOException {
        // 1.创建SearchSourceBuilder对象
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
        //  1.1.添加查询条件QueryBuilders，需要分词搜索等，但不能查询全部
        sourceBuilder.query(QueryBuilders.matchQuery("note", "唱歌表演"));

        //添加高亮：指定高亮字段,这里是note字段
        sourceBuilder.highlighter(new HighlightBuilder().field("note"));

        // 2.创建SearchRequest对象，并制定索引库名称
        SearchRequest request = new SearchRequest("user");
        // 3.添加SearchSourceBuilder对象到SearchRequest对象中
        request.source(sourceBuilder);
        // 4.发起请求，得到结果
        SearchResponse response = client.search(request, RequestOptions.DEFAULT);
        // 5.解析结果
        SearchHits searchHits = response.getHits();
        //  5.1.获取总条数
        long total = searchHits.getTotalHits().value;
        System.out.println("total = " + total);
        //  5.2.获取SearchHits数组，并遍历
        SearchHit[] searchHit = searchHits.getHits();
        for (SearchHit hit : searchHit) {
            //获取分数
            System.out.println("文档得分："+hit.getScore());
            //  - 获取其中的`_source`，是JSON数据
            String source = hit.getSourceAsString();
            //将json反序列化为java对象
            User user = JSON.parseObject(source, User.class);
            System.out.println("没有高亮的user = " + user);

            //获取高亮结果
            //获取当前文档的所有高亮了的字段结果，如果没有被高亮的，则不再这个集合中
            Map<String, HighlightField> highlightFields = hit.getHighlightFields();
            // 根据字段名，获取高亮字段（只能取到高亮了的字段）
            HighlightField highlightField = highlightFields.get("note");
            //获取到高亮片段（段落）数组
            Text[] fragments = highlightField.getFragments();
            //将高亮片段拼接到一起
            String noteHighlight = StringUtils.join(fragments);
            // 覆盖旧值(即使用高亮的值覆盖没高亮的值)
            user.setNote(noteHighlight);

            System.out.println("有高亮的user = " + user);
        }
    }

```

有可能出现该效果，建立索引的时候，该字段没有指定ik分词器：

![image-20200220153003685](assets/image-20200220153003685.png)



## 度量查询

```java
	/**
     * 度量查询
     	需求： 查询全部数据   
     	       按照性别分组  
     	       并统计每个分组的：人数、最大年龄 最小年龄 平均年龄  总年龄
     		
     * @throws IOException
     */
    @Test
    public void searchForAggs() throws IOException {
        // 创建请求对象
        SearchRequest searchRequest = new SearchRequest("user");
        SearchSourceBuilder builder = new SearchSourceBuilder();
        QueryBuilder queryBuilder = QueryBuilders.matchAllQuery();
        // 构建查询条件
        builder.query(queryBuilder);
        // 设置聚合分组   按照关键词在gender字段上分组
        TermsAggregationBuilder termGroup = AggregationBuilders.terms("my_gender").field("gender");
        // 设置分组后的度量计算  计算类型stats 主要计算年龄
        termGroup.subAggregation(
  AggregationBuilders.stats("my_stats").field("age")
        );
        
        //构建聚合条件
        builder.aggregation(termGroup);
        searchRequest.source(builder);
        // 执行请求，得到结果
        SearchResponse response = client.search(searchRequest, RequestOptions.DEFAULT);
        SearchHits hitDocs = response.getHits();
        // 处理过后的分组结果  Terms 为分桶的子接口
        Terms terms = response.getAggregations().get("my_gender");
        // 获取里面的桶信息
        List<? extends Terms.Bucket> buckets = terms.getBuckets();
        // 循环遍历每个桶
        for (Terms.Bucket bucket : buckets) {
            // 该桶文档个数
            long docCount = bucket.getDocCount();
            // 桶的名称
            Object key = bucket.getKey();
            System.out.println("小组名称==>"+key);
            System.out.println("小组数量==>"+docCount);
            // 使用对应度量类型子类接收度量结果
            Stats my_stats = bucket.getAggregations().get("my_stats");
            // 获取分析后的值
            System.out.println("平均值==>"+my_stats.getAvg());
            System.out.println("最大值==>"+my_stats.getMax());
            System.out.println("最小值==>"+my_stats.getMin());
            System.out.println("求和==>"+my_stats.getSum());
        }
        long totalHits = hitDocs.getTotalHits().value;
        System.out.println("查询的总记录数:"+totalHits);
        SearchHit[] hits = hitDocs.getHits();
        for (SearchHit hit : hits) {
            String sourceAsString = hit.getSourceAsString();
            System.out.println(sourceAsString);
        }
//        System.out.println(response);
    }
```

# 二. Windows下的集群部署

我们之前安装的是单机的ES，线上部署肯定不能只用一台，因为一旦服务宕机，整个搜索服务就不可用了。因此必须使用集群来解决。

那么问题来了：什么是集群呢？



## 集群简介

## 什么是集群

来看下维基百科对集群的介绍：

![1543286678122](assets/1543286678122.png)

集群是==一组计算机==高度紧密协作，完成计算工作。

其中的每个计算机称为一个==节点==。

## ES集群概念

单点的elasticsearch存在哪些可能出现的问题呢？

- 单台机器存储容量有限
- 单服务器容易出现单点故障，无法实现高可用
- 单服务的并发处理能力有限

所以，为了应对这些问题，我们需要对elasticsearch搭建集群

### 数据分片

首先，我们面临的第一个问题就是数据量太大，单点存储量有限的问题。

我们可以把数据拆分成多份，每一份存储到不同机器节点（node），从而实现减少每个节点数据量的目的。这就是数据的分布式存储，也叫做：`数据分片（Shard）`。

![image-20200104124440086](assets/image-20200104124440086.png)

此处，我们把数据分成3片：shard0、shard1、shard2

### 副本备份

数据分片解决了海量数据存储的问题，但是如果出现单点故障，那么分片数据就不再完整，这又该如何解决呢？

没错，就像大家为了备份手机数据，会额外存储一份到移动硬盘一样。我们可以给每个分片数据进行备份，存储到其它节点，防止数据丢失，这就是数据备份，也叫`数据副本（replica）`。



数据备份可以保证高可用，但是每个分片备份一份，所需要的节点数量就会翻一倍，成本实在是太高了！

为了在高可用和成本间寻求平衡，我们可以这样做：

- 首先对数据分片，存储到不同节点
- 然后对每个分片进行备份，放到对方节点，完成互相备份

这样可以大大减少所需要的服务节点数量，如图，我们以3分片，每个分片备份一份为例：

![image-20200104124551912](assets/image-20200104124551912.png)

现在，每个分片都有1个备份，存储在3个节点：

- node0：保存了分片0和1
- node1：保存了分片0和2
- node2：保存了分片1和2

## 集群搭建

进入ElasticSearch的bin目录：

![image-20200104121859074](assets/image-20200104121859074.png) 

然后打开**3个控制台**，分别输入下面的3个指令：

```
# node0指令：
elasticsearch.bat -E node.name=node0 -E cluster.name=elastic -E path.data=node0_data


# node1指令：
elasticsearch.bat -E node.name=node1 -E cluster.name=elastic -E path.data=node1_data


# node2指令：
elasticsearch.bat -E node.name=node2 -E cluster.name=elastic -E path.data=node2_data


```

命令解释：

- `elasticsearch.bat`：运行elasticsearch.bat文件
- ` -E node.name`：`-E`是环境参数，node.name是指定节点名称
- `cluster.name`：指定集群名称
- `path.data`：指定数据目录地址，相对路径是相对于ElasticSearch的安装目录，默认是data。



提示：http端口默认是9200，transport端口默认是9300，如果被占用了，则会自动+1，即9201、9202...

注意：如果磁盘空余的空间少于85%，则该节点不会成为副本节点，控制台会打印如下信息：

```
[2020-02-13T22:55:40,151][INFO ][o.e.c.r.a.DiskThresholdMonitor] [node0] low disk watermark [85%] exceeded on [E8HetTg6T-CCp0WjV_p73A][node0][D:\00beike\elasticsearch-7.5.1\node0_data\nodes\0] free: 37.6gb[14.9%], replicas will not be assigned to this node

```



## 配置Kibana访问集群

### 修改配置

在Kibana中的config中打开kibana.yml文件：

![image-20200104123006061](assets/image-20200104123006061.png) 

找到这样一行代码：

```yml
# elasticsearch.hosts: ["http://localhost:9200"]
```

前面的`#`是注释，需要删除以打开注释。然后在后面的数组中添加ES的集群地址：

```yaml
elasticsearch.hosts: ["http://localhost:9200","http://localhost:9201","http://localhost:9202"]
```



### 重启并访问

重启kibana，然后再左侧的菜单点击monitor：

![image-20200104123219341](assets/image-20200104123219341.png) 

在页面中点击按钮，![image-20200213230257542](assets/image-20200213230257542.png)

然后，再打开监控功能，可能需要等待几秒钟。

![image-20200104123234801](assets/image-20200104123234801.png)

然后可以看到kibana提供的监控功能：

![image-20200104123303523](assets/image-20200104123303523.png)

点击“Nodes:3”可以看到启动的3个节点的信息：

![image-20200104123425983](assets/image-20200104123425983.png)



## 测试集群

### 配置分片和副本信息

还记得创建索引库的API吗？

- 请求方式：PUT

- 请求路径：/索引库名

- 请求参数：json格式：

  ```json
  {
      "settings": {
          "属性名": "属性值"
        }
  }
  ```

  settings：就是索引库设置，其中可以定义索引库的各种属性，之前我们没有配置，是默认值。

settings中就可以配置索引库的分片和副本信息，如下：

```json
#若索引存在，则先删除索引
#DELETE /heima
#创建索引并指定分片和副本
PUT /heima
{
  "settings": {
    "number_of_shards": 3, 
    "number_of_replicas": 1 
  }
}

```

这里有两个配置：

- number_of_shards：分片数量，这里设置为3，不指定则默认为1。
- number_of_replicas：副本数量，这里设置为1，每个分片一个备份，一个原始数据，共2份。不指定默认为1。



### 查看分片结果

进入monitor页面，然后选择查看索引库（indices)信息：

![image-20200104123901902](assets/image-20200104123901902.png)

可以看到我们刚刚加入的索引库：

![image-20200104124032092](assets/image-20200104124032092.png)

点击进入，然后拉到页面最底部：

![image-20200104124127516](assets/image-20200104124127516.png)

可以看到每个分片在节点上的信息：

- node0：保存了分片0和1
- node1：保存了分片0和2
- node2：保存了分片1和2

这个结果与我们上面画图分析是一致的。片的颜色代表不同的状态。



### 集群动态伸缩

现在，我们让node1宕机，停止node1的控制台进程即可。

然后查看Kibana中的节点状态：

![image-20200104124825356](assets/image-20200104124825356.png)

发现node1已经宕机了，此时数据是不安全的。

稍等片刻，再次查看：

![image-20200104125003684](assets/image-20200104125003684.png)

发现分片数据进行了重新分配，node1上的分片被重新分配了。此时集群依然是健康的。



我们重新启动node1看看：

![image-20200104125153682](assets/image-20200104125153682.png)

分片再次重新分配，那么每个节点都存储了部分分片。