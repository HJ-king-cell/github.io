# MYSQL基础

## MySQL元数据库information_schema（5版本以后才有）


  <figure class="thumbnails">
    <img src="assets/img/元数据库.png" alt="Screenshot of coverpage" title="Cover page">
    
</figure>

## MySQL语句解析


  <figure class="thumbnails">
    <img src="assets/img/解析.png" alt="Screenshot of coverpage" title="Cover page">
    
</figure>


## MySQL常用内置函数

| 函数名称 | 函数功能 | 
| :------ | :------ |
| system.user() |  系统用户名 |
| user() |  用户名 |
| current_user()  | 当前用户名 |
| session_user() |连接数据库的用户名    |
|    database()  |数据库名       |
|version()  |数据库版本   |
|@@datadir   |数据库路径   |
|@@basedir  |数据库安装路径  |
|@@version_compile_OS   |操作系统 |
|count()  |返回执行结果数量   |
| concat()  |没有分隔符的连接字符串  |
|concat_ws()  |含有分割符的连接字符串   |
| group_concat()  |连接一个组的所有字符串，并以逗号分割每一条数据   |
|load_file()  |读取本地文件    |
| into outfile  | 写文件  |
|ascii()  |字符串的ASCII码值 |
| ord()  | 返回字符串第一个字符的ASCII码值  |
| mid() | 返回一个字符串的一部分   |
| substr()  | 返回一个字符串的一部分 |
| length() | 返回支付串的长度   |


## MYSQL常用函数

| 函数名称 | 函数功能 | 
| :------ | :------ |
|left()  |返回字符串最左边的几个字符   |
|floor()  |返回小于或等于x的最大整数   |
|rand()  |返回0和1之间的一个随机数   |
|extractvalue()  |从目标XML中返回包含所查询值的字符串   |
|updatexml()  |改变文档中符合提哦啊见的节点的值   |
|sleep()  |让此语句运行N秒钟   |
|if()  |判断   |
| char() |返回整数ASCII代码字符组成的字符串   |
| STRCMP() | 比较字符串内容  |
| IFNULL()|例如参数1不为NULL,则返回值为参数1，否则返回参数2   |
| exp() |返回e的x次方   |



## MYSQL常用函数讲解
- substr()函数

```
substr(string, start,<length>)          #从string的start位置开始提取字符串 

length:要提取字符串的长度，若length为以下任意条件之一时，返回start位置到串尾的所有字符:
length不指定 
length为空 
length为负数
length大于start到串尾的长度
```


```
ascii(substr((select user)),1,1))=114     

ascii(
    substr(     (select user)),1,1)   )
)=114
#查看用户名从一个字符开始截取一个，判断ASCII码是否是114
```
- if(ascii(substr((select user)),1,1))=114,0,sleep(5))
```
if(
    ascii(substr((select user)),1,1))=114
    ,0
    ,sleep(5)
    
    )     
#查看用户名从一个字符开始截取一个，ASCII码不是114，返回0，是睡眠5s
```

- limit(a,b)
```
从a开始，查b个
```


## Plugin Styles

All [docsify plugins](https://docsify.js.org/#/plugins) will work with *docsify-themeable*, however there are two potential issues to be aware of:

- **Not all plugins support multiple themes**

  Plugins often inject CSS to style plugin-related elements. Ideally, a plugin's CSS would inherit visual styles from the current theme. This isn't always practical, so some plugins will apply visual styles intended to match a specific theme (typically the default `vue.css` theme). The result is color, typography, and layout styles that do not match your site theme.

- **Not all plugins support theme customization**

  Many [docsify plugins](https://docsify.js.org/#/plugins) were developed prior to the release of *docsify-themeable* and therefore do not offer theme customization. To address this issue, *docsify-themeable* provides customizable theme properties for many popular docsify plugins (below). If you would like to see theme properties added to a plugin, create a [Github issue](https://github.com/jhildenbiddle/docsify-themeable/issues) with the plugin name and a link to the source code.

### Copy Code

Theme properties for [docsify-copy-code](https://github.com/jperasmus/docsify-copy-code) plugin.

[_plugin-copycode.css](https://cdn.jsdelivr.net/npm/docsify-themeable@0/src/scss/themes/defaults/_plugin-copy-code.css ':include')

### Pagination

Theme properties for [docsify-pagination](https://github.com/imyelo/docsify-pagination) plugin.

[_plugin-pagination.css](https://cdn.jsdelivr.net/npm/docsify-themeable@0/src/scss/themes/defaults/_plugin-pagination.css ':include')

### Search

Theme properties for docsify's [search](https://docsify.js.org/#/plugins?id=full-text-search) plugin.

[_plugin-search.css](https://cdn.jsdelivr.net/npm/docsify-themeable@0/src/scss/themes/defaults/_plugin-search.css ':include')

### Tabs

See the [docsify-tabs site](https://jhildenbiddle.github.io/docsify-tabs/) for a complete list of theme properties.

The following theme properties overrides are included with docsify-themeable:

[_plugin-docsify-tabs.css](https://cdn.jsdelivr.net/npm/docsify-themeable@0/src/scss/themes/defaults/_plugin-docsify-tabs.css ':include')

### Zoom Image

Theme properties for docsify's [zoom image](https://docsify.js.org/#/plugins?id=zoom-image) plugin.

[_plugin-zoom.css](https://cdn.jsdelivr.net/npm/docsify-themeable@0/src/scss/themes/defaults/_plugin-zoom-image.css ':include')

## PrismJS

[PrismJS](http://prismjs.com/) is the syntax highlighter used by [docsify](https://docsify.js.org/) for styling code blocks. Styles can be customized by setting [`--code`](#-code) theme properties or by using one of the many [PrismJS themes](https://cdn.jsdelivr.net/npm/prismjs/themes/) available.

To use a PrismJS theme, add a `<link>` to your `index.html` after your site theme:

```html
<!-- Site theme -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-defaults.min.css">

<!-- PrismJS theme -->
<link rel="stylesheet" href="path/to/prismjs-theme.css">
```

Note that only PrismJS theme colors will be applied. Layout and typography styles such as `font-family`, `border-radius`, `margin` and `padding` will continue to be applied by the site theme to maintain visual consistency.

The [theme properties](#theme) that override PrismJS theme values are:

- `--code-font-family`
- `--code-font-size`
- `--code-font-weight`
- `--code-block-border-radius`
- `--code-block-line-height`
- `--code-block-margin`
- `--code-block-padding`
