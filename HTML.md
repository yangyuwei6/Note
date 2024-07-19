# HTML基础学习

## 标记语法

### 封闭类型标记：也叫双标记

注意：封闭标记必须成对出现，在结束标记里，先写一个/，再写标记名称如果没有在结束标记里写/，会报错

```html
//语法
<标记>内容</标记>
```

### 非封闭类型标记：也叫单标记

注意：非封闭标记不包含内容

```html
　//语法
　<标记/> 或 <标记>
```

## 网页基本信息

### html标签

定义HTML文档，这个元素我们浏览器看到后就明白这是个HTML文档了，所以你的其它元素要包裹在他里面，标签限定了文档的开始点和结束点

```html
<!DOCTYPE html>
<html>
    ...
</html>
```

### head标签

head标签用于定义文档 的头部。文档的头部描述了文档的各种属性和信息，包括文档的标题、在Web中的位置以及其他文档的关系等。绝大多数文档头部包含的数据都不会真正作为内容显示给读者

```html
<!DOCTYPE html>
<html>
    <head>
        ...
    </head>
</html>
```

### body标签

 body元素包含文档的所有内容（比如文本、超链接、图像、表格和列表等等），定义文档的主体，它会直接在页面中显示出来，也就是用户可以直观看到的内容

```html
<!DOCTYPE html>
<html>
    <head>
        ...
    </head>
    <body>
        ...
    </body>
</html>
```

### title标签

定义文档的标题 ,它显示在浏览器窗口的标题栏或状态栏上,title标签是head标签中唯一必须要求包含的东西，就是说写head一定要写title

```html
<!DOCTYPE html>
<html>
    <head>
        <!--title显示在浏览器窗口的标题栏或状态栏上-->
        <title>文档标题</title>
    </head>
    <body>
        ...
    </body>
</html>
```

### meta标签

meta标签用来描述一个HTML网页文档的属性，关键词等

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <!--显示在浏览器窗口的标题栏或状态栏上-->
        <title>文档标题</title>
    </head>
    <body>
        ...
    </body>
</html>
```

## 网络基本标签

### 标题标签

标题是通过<h1>-<h6>标签进行定义的。<h1>定义最大的标题<h6>定义最小的标题

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <!--显示在浏览器窗口的标题栏或状态栏上-->
        <title>文档标题</title>
    </head>
    <body>
        <h1>一级标题</h1>
        <h2>二级标题</h2>
        <h3>三级标题</h3>
        <h4>四级标题</h4>
        <h5>五级标题</h5>
        <h6>六级标题</h6> 
    </body>
</html>
```

### 段落标签

段落是通过<p>标签定义的

```html
<p>yyw</p>
```

### 换行标签

<br>换行标签，<br>是一个单标签

```html
<br>或者<br/>或者</br>
```

### 水平线标签

```html
<hr/>
```

### 字体样式标签

```html
<!--粗体-->
<strong>内容</strong>
<!--斜体-->
<em>内容</em>
```

### 注释和特殊符号

特殊符号随时用随时查询

```html
<!-- 注释内容 -->

<!-- 空格 -->
内容&nbsp;内容

<!-- 大于号 -->
&gt;

<!-- 小于号 -->
&lt;

<!-- 版权符号 -->
&copy;

```

## 超链接标签及应用

HTML使用标签<a>来设置超文本链接，可以跳转到新的文档或者当前文档中的某个部分

```html
<a href="链接路径" target="目标窗口位置">链接文本或图像</a>
<!--示例-->
<a href="https://www.baidu.com">点击我跳转到目标网页</a>
```

### href

href是HTML的一个属性，全称为Hypertext Reference，href属性的值可以是一个URL（统一资源定位符），也可以是一个文件路径或一个仅包含文本的HTML元素的ID

### target

target用于控制目标页面以何种方式打开

当前窗口打开：target="_self" 

打开新窗口：target=”blank"

打开上一级窗口：target=”_parent"

打开整个窗口：target=”_top"

锚链接实现回到顶部

```html
<a name:"top">顶部</a>
...
....
...
<a href="#top">回到顶部</a>
```



## 列表标签

### 无序列表

无序列表使用<ul>标签表示，列表项使用<li>标签表示

```html
<ul>
<li>列表项1</li>
<li>列表项2</li>
<li>列表项3</li>
</ul>
```

### 有序列表

有序列表使用<ol>标签表示，列表项同样使用<li>标签表示

```html
<ol>
<li>列表项1</li>
<li>列表项2</li>
<li>列表项3</li>
</ol>
```

### 自定义列表

自定义列表使用<dl>标签表示，列表名称用<dt>表示，列表内容用<dd>表示

```html
<dl>
    <dt></dt>
    <dd></dd>
    <dd></dd>
</dl>
```

## 表格标签

### 标签说明

```html
<!--表格标签-->
<table></table>

<!--表格行标签-->
<tr></tr>

<!--表格列标签-->
<td></td>

<!--表头标签-->
<th></th>

<!--表格标题-->
<caption></caption>

<!--表格列所属的组-->
<colgroup></colgroup>

<!--表格列的属性-->
<col>

<!--表格页眉-->
<thread></thread>

<!--表格页脚-->
<tfoot></tfoot>

<!--表格的主题-->
<tbody></tbody>

```

### 属性说明

```html
<!--表格的边框长度，单位是像素-->
<border></border>

<!--多行合并，可以指定具体要合并的行数-->
<rowspan></rowspan>

<!--多列合并，可以指定具体要合并的列数-->
<colspan></colspan>

<!--单元格边缘与其单元格内容之间的空白间距-->
<cellpanding></cellpanding>

<!--单元格之间的空白键距-->
<cellspacing></cellspacing>

<!--表格的宽度-->
<width></width>

<!--表格的摘要-->
<summary></summary>

```



## 媒体元素

### 静态资源

```go
//加载媒体元素前必须加载静态资源
r.Static("/static", "./static")
```

### 图像

```html
<!--属性：src：图片路径、alt：规定图像的代替文本、width：规定图像的宽度、height：规定图像的高度、title：鼠标悬停在图片上给予提示-->
<img src="xx" alt="xx" width-"xx" height="xx" title="xx">
```

### 视频

```html
<video src="C:/Users/Administrator/Desktop/4.mp4" controls autoplay></video>
```

### 音频

```html
<audio src="C:/Users/Administrator/Desktop/4.mp4" controls autoplay></audio>
```

## 页面结构分析

```html
<!--标记头部区域的内容 -->
<header></header>

<!-- 标记脚部区域的内容-->
<footer></footer>

<!--Web页面中的一块独立区域-->
<section></section>

<!--独立的文章内容-->
<article></article>

<!--相关内容或应用（常用于侧边栏）-->
<aside></aside>

<!--导航内辅助内容-->
<nav></nav>
```

## iframe内联框架

```html
<iframe src="path" name="框架表示名" frameboader="1|0"</iframe>
```

## 表单

可用于登录和注册

```html
<!--method规定如何发送表单数据，action表示向何处发送表单数据-->
<form method="post|get" action="result.html">
      <p> 名字：<input name="name" type="txt"></p>
      <p> 密码：<input name="pass" type="passwprd"></p>
      <p>
          <input type="submit" name="button" value="提交"/>
          <input type="reset" name="Reset" value="重填"/> 
      </p> 
</form>
```

### inpute标签

input是一个输入框标签，用于指定用户可以在其中输入数据的输入字段；输入字段可通过多种方式改变，取决于标签内的type属性

#### inpute属性

type:指定元素的类型。如text，password，checkbox，radio，submit，reset，file，hidden，image，button。默认为text

name：指定表单元素的名称

value:元素的初始值，type为radio时必须指定一个值

size：指定表单元素的初始宽度。当type为text或password时，表单元素的大小以字符为单位。其他类型宽度以像素为单位

maxlength：type为text或text时，输入的最大字符数

checked：type为radio或者checkbox时，指定按钮是否是被选中

## 多选框

```html
<p>爱好
    <input type="checkbox" name="ball" value="football">
    <input type="checkbox" name="ball" value="basketball">
    <input type="checkbox" name="ball" value="vollyball">
    <input type="checkbox" name="ball" value="baseball">
</p>

```

## 按钮

```html
<button type="submit" name="submit">
    提交
</button>
```

## 下拉框

```html
<select name="area">
    <option value="China">中国</option>
    <option value="American">美国</option>
    <option value="Russia">俄罗斯</option>
    <option value="England">英国</option>
</select>
```

## 简单验证

```html
<!--邮箱验证-->
<p>邮箱验证
    <input type="email" name="email">
</p>
<!--url验证-->
<p>url验证
    <input type="url" name="url">
</p>
```

## 搜索框滑块

```html
<p>滑块
    <input type="range" name="voice" min="1" max="30">
</p>
```

# 