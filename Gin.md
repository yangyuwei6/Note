#  Gin

官方文档地址：http://gin-gonic.com/zh-cn/docs/

## Gin框架安装和使用

### 1.创建项目，搭建环境

![image-20240520142646641](https://raw.githubusercontent.com/yangyuwei6/photo/main/image/202407191130064.png)

```go
Environment：GOPROXY=https://goproxy.cn,direct
```



### 2.安装

```
终端输入：go get -u github.com/gin-gonic/gin
```

![image-20240520142803717](https://raw.githubusercontent.com/yangyuwei6/photo/main/image/202407191130320.png)

### 3.导包

```go
import "github.com/gin-gonic/gin"   
import "net/http"   //项目中使用了 http.StatusOK
```

### 4.示例

```go
package main

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

func main() {
    /**
    所有的接口都要由路由来进行管理。
        Gin的路由支持GET,POST,PUT,DELETE,PATCH,HEAD,OPTIONS等请求
        同时还有一个Any函数，可以同时支持以上的所有请求。

    创建路由(router)并引入默认中间件
        router := gin.Default()
        在源码中,首先是New一个engine,紧接着通过Use方法传入了Logger()和Recovery()这两个中间件。
        其中 Logger 是对日志进行记录，而 Recovery 是对有 painc时, 进行500的错误处理。

    创建路由(router)无中间件
        router := gin.New()
    */
    router := gin.Default()

    //加载模版文件
    router.LoadHTMLGlob("./templates/*")

    //加载静态资源
    router.Static("/statics", "./statics")

    //定义GET方法
    router.GET("/index", func(context *gin.Context) {
        /**
        http.StatusOK:
            响应的状态码,对应常量200.
        "index.tmpl":
            前端模板的名字。

        温馨提示:
            以"*.tmpl"结尾的文件是Golang针对前端页面进行定义的格式,事实上它也是一种模板引擎(比如逻辑运算,判断,循环，变量,过滤等功能)。
        */
        //gin.H==map[string]interface{}
        context.HTML(http.StatusOK, "index.tmpl", gin.H{
            //改变量在"index.tmpl"中是有定义的，可以通过相同变量名拿到对应的数据哟~
            "title": "Hello World !",
        })
    })

    //启动路由并指定监听的地址及端口，若不指定默认监听0.0.0.0:8080
    router.Run("172.30.100.101:9000")
}
```

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)
func main() {
	//创建默认路由引擎
	engine := gin.Default()
	//配置路由
    // GET：请求方式；/ping：URL
	// 当客户端以GET方法请求/ping路径时，会执行后面的匿名函数
	engine.GET("/ping", func(context *gin.Context) {
        //返回JSON格式的数据
		context.JSON(http.StatusOK, gin.H{
			"message": "hello gin",
		})
	})
	//启动web服务
	//engine.Run() //开启服务器，默认监听localhost:8080
	//指定端口
	engine.Run(":8000")
}

```

```go
package main

import "github.com/gin-gonic/gin"

func hello(c *gin.Context) {
	c.JSON(200, gin.H{"name": "yyw"})
}
func main() {
	r := gin.Default()
	r.GET("/ping", hello)//外部定义hello替代匿名函数
	r.Run(":8080")
}
```

gin.default()

```go
//返回一个引擎-框架核心-默认服务器
func Default(opts ...OptionFunc) *Engine {
	debugPrintWARNINGDefault()
	engine := New()
	engine.Use(Logger(), Recovery())
	return engine.With(opts...)
}
```

cmd ipconfig可以得到ip地址

## Gin模板渲染

### 输出格式

```go
//返回json格式
func (c *Context) JSON(code int, obj interface{})
//返回xml格式
func (c *Context) XML(code int, obj interface{})
//返回yaml格式
func (c *Context) YAML(code int, obj interface{})
//返回string格式
func (c *Context) String(code int, format string, values ...interface{})
//渲染html模板后返回
func (c *Context) HTML(code int, name string, obj interface{})
```

### Gin HTML模板渲染

#### Gin加载不同层级目录下的html

Gin提供两种方法加载html模板

```
//r.LoadHTMLGlob("template/**/*")
此种可以加载tmplate下级目录下的所有文件
//r.LoadHTMLFiles("template/admin/file.html")
参数用project下的路径
```

```
engine.HTML(状态码，模板名称，空接口)
```

后端页面代码：

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/thinkerou/favicon"
	"net/http"
)

func main() {
	//创建gin引擎

	engine := gin.Default()
	engine.LoadHTMLGlob("template/*")
    //响应一个页面给前端
	engine.GET("/index", func(c *gin.Context) {
        //渲染模板
		c.HTML(http.StatusOK, "index", gin.H{
			"msg": "这是go后台传来的数据",
		})
	})
	engine.Run(":8000")
}
//在浏览器中输入：localhost：8000/index 即可看到输出
```

前端页面代码：

```html
<!DOCTYPE html>
<!--语言是英语-->
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>我的一个go web 页面</title>
</head>
<body>
<h1>欢迎访问我的网站</h1>

获取的数据为：
{{.msg}}
</body>
</html>
```

```
在html文件中用{{.属性}}获取从后端传给前端的值
main文件中
engine.GET("/index", func(c *gin.Context) {
		c.HTML(http.StatusOK, "index", gin.H{
			"msg": "这是go后台传来的数据",
		})
	})
```

#### 添加网站头像

```go
//需要 get github.com/thinkerou/favicon
package main
import (
	"github.com/gin-gonic/gin"
	"github.com/thinkerou/favicon"
	"net/http"
)

func main() {
    
    
	//创建gin引擎

	engine := gin.Default()
	engine.Use(favicon.New("favicon.ico"))

	//配置路由
	engine.GET("/ping", func(context *gin.Context) {
		context.JSON(http.StatusOK, gin.H{
			"message": "hello gin",
		})
	})
	//启动web服务
	//engine.Run() //开启服务器，默认监听localhost:8080
	//指定端口
	engine.Run(":8000")
}

```

#### 不同路径下的同名模板

前端页面代码：

```html
<!--相当于给模板定义一个名称 define end 成对出现-->
{{define "default/index.html" }}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>html渲染练习demo</title>
</head>
<body>
这是default文件夹下的index
{{.title}}
<br>
{{.person}}
</body>
</html>
{{end}}
```

后端页面代码：

```go
package main
import (
	"github.com/gin-gonic/gin"
	"net/http"
)
type Person struct {
	name string
	age  int
}
func main() {
	r := gin.Default()
	r.LoadHTMLGlob("template/**/*")
	r.GET("admin/index", func(c *gin.Context) {
		person := &Person{
			name: "yyw",
			age:  20,
		}
		c.HTML(http.StatusOK, "admin/index.html", gin.H{
			"title":  "Goland Projects",
			"person": person,
		})
	})
	r.GET("default/index", func(c *gin.Context) {
		person := &Person{
			name: "yyw",
			age:  20,
		}
		c.HTML(http.StatusOK, "default/index.html", gin.H{
			"title":  "Goland Projects",
			"person": person,
		})
	})

	r.Run(":8000")
}
```

#### 模板相关语法

##### HTML中加载静态文件

```go
r.Static("/static", "./static")
```

##### 变量的定义和使用

```html
<!--定义变量-->
{{$obj:=.属性}}
<!--使用变量-->
{{4obj}}
```

##### 比较函数

```html
<!--ge 和. 之前的空格不可以省略-->
{{if ge .score 60}}
<p>及格</p>
{{else}}
<p>不及格</p>
{{end}}
ge 大于等于
gt 大于
lt 小于
le 小于等于
eq 等于
ne 不等于
```

##### 遍历切片

```html
<!--后端传入一个切片，在前端遍历-->
<ul>
    {{ range $key,$val:=.hobby}}
    <li>{{$val}}</li>
    {{end}}
</ul>
```

##### with结构结构体

```html
<!--相当于把with obj 赋值给.-->

```

### Gin json模板渲染

```go
func main() {
	r := gin.Default()

	// gin.H 是map[string]interface{}的缩写
	r.GET("/someJSON", func(c *gin.Context) {
		// 方式一：自己拼接JSON
		c.JSON(http.StatusOK, gin.H{"message": "Hello world!"})
	})
	r.GET("/moreJSON", func(c *gin.Context) {
		// 方法二：使用结构体
		var msg struct {
			Name    string `json:"user"`
			Message string
			Age     int
		}
		msg.Name = "小王子"
		msg.Message = "Hello world!"
		msg.Age = 18
		c.JSON(http.StatusOK, msg)
	})
	r.Run(":8080")
}
```

### Gin xml渲染

```go
func main() {
	r := gin.Default()
	// gin.H 是map[string]interface{}的缩写
	r.GET("/someXML", func(c *gin.Context) {
		// 方式一：自己拼接JSON
		c.XML(http.StatusOK, gin.H{"message": "Hello world!"})
	})
	r.GET("/moreXML", func(c *gin.Context) {
		// 方法二：使用结构体
		type MessageRecord struct {
			Name    string
			Message string
			Age     int
		}
		var msg MessageRecord
		msg.Name = "小王子"
		msg.Message = "Hello world!"
		msg.Age = 18
		c.XML(http.StatusOK, msg)
	})
	r.Run(":8080")
}
```



## 获取参数

### Gin获取querystring参数

获取浏览器中的query参数

localhost：8080/web?query=杨超越

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)
func main() {
	r := gin.Default()
	r.GET("/web", func(c *gin.Context) {
		name := c.Query("query")
		c.JSON(http.StatusOK, gin.H{
			"name": name,
		})
	})
	r.Run(":8080")
}
```

输出：name=杨超越

### Gin获取form参数

后端页面代码：

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)
func main() {
	r := gin.Default()
	r.LoadHTMLGlob("template/**/*")
	r.GET("/login", func(c *gin.Context) {
		c.HTML(http.StatusOK, "login.html", gin.H{})
	})
	r.POST("/login", func(c *gin.Context) {
		//处理html文件中的post请求
		name := c.PostForm("name")
		password := c.PostForm("password")
		c.HTML(http.StatusOK, "ans.html", gin.H{
			"name":     name,
			"password": password,
		})
	})
	r.Run(":8800")
}
```

前端页面代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>form表单</title>
</head>
<body>

<form action="/login" method="post">
    <p>名字： <input name="name" type="txt"> </p>
    <p>密码： <input name="password" type="password">
        <!--action:点击submit时向/login提交post请求-->
    <p>      <input type="submit" name="button" value="提交">
             <input type="reset" name="Reset" value="重填">
    </p>
</form>
</body>
</html>
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ans</title>
</head>
<body>
用户名是{{.name}}
密码是{{.password}}
</body>
</html>
```

### Gin获取path参数

```go
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/pr/:name/:age", func(c *gin.Context) {
		name := c.Param("name")
		age := c.Param("age")
		c.JSON(200, gin.H{
			"name": name,
			"age":  age,
		})
	})
	r.Run(":9090")
}
```

```
浏览器输入： http://localhost:9090/pr/yang/18
输出：{"age":"18","name":"yang"}
```

1.c.param?这个函数详细了解下

2.gin获取path参数这个一般会应用在什么场景下

### Gin参数绑定

```go
// Binding from JSON
type Login struct {
	User     string `form:"user" json:"user" binding:"required"`
	Password string `form:"password" json:"password" binding:"required"`
}

func main() {
	router := gin.Default()
    //在postman里面实现
	// 绑定JSON的示例 ({"user": "q1mi", "password": "123456"})
	router.POST("/loginJSON", func(c *gin.Context) {
		var login Login

		if err := c.ShouldBind(&login); err == nil {
			fmt.Printf("login info:%#v\n", login)
			c.JSON(http.StatusOK, gin.H{
				"user":     login.User,
				"password": login.Password,
			})
		} else {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		}
	})
    //在postman里面实现
	// 绑定form表单示例 (user=q1mi&password=123456)
	router.POST("/loginForm", func(c *gin.Context) {
		var login Login
		// ShouldBind()会根据请求的Content-Type自行选择绑定器
		if err := c.ShouldBind(&login); err == nil {
			c.JSON(http.StatusOK, gin.H{
				"user":     login.User,
				"password": login.Password,
			})
		} else {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		}
	})
    //可以在浏览器实现
	// 绑定QueryString示例 (/loginQuery?user=q1mi&password=123456)
	router.GET("/loginForm", func(c *gin.Context) {
		var login Login
		// ShouldBind()会根据请求的Content-Type自行选择绑定器
		if err := c.ShouldBind(&login); err == nil {
			c.JSON(http.StatusOK, gin.H{
				"user":     login.User,
				"password": login.Password,
			})
		} else {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		}
	})

	// Listen and serve on 0.0.0.0:8080
	router.Run(":8080")
}
```



## Gin文件上传

### 单个文件上传

前端代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>单个文件上传</title>
</head>
<body>
<form action="/src" method="post" enctype="multipart/form-data">

    <input type="file" name="f1">
    <input type="submit" value="上传">
</form>
</body>
</html>
```

后端代码：

```go
/*
FormFile returns the first file for the provided form key.
func (c *Context) FormFile(name string) (*multipart.FileHeader, error) {
	if c.Request.MultipartForm == nil {
		if err := c.Request.ParseMultipartForm(c.engine.MaxMultipartMemory); err != nil {
			return nil, err
		}
	}
	f, fh, err := c.Request.FormFile(name)
	if err != nil {
		return nil, err
	}
	f.Close()
	return fh, err
}
*/
```



```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
	"path"
)
func main() {
	r := gin.Default()
    //加载模板
	r.LoadHTMLFiles("template/admin/file.html")
    //处理get
	r.GET("/src", func(c *gin.Context) {
        //渲染模板
		c.HTML(200, "file.html", nil)
	})
    //处理html文件里面的post
	r.POST("/src", func(c *gin.Context) {
		fp, err := c.FormFile("f1")
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{
				"message": err.Error(),
			})
		} else {
			destination := path.Join("./", fp.Filename)
			c.SaveUploadedFile(fp, destination)
			c.JSON(200, gin.H{
				"msg": "ok",
			})
		}
	})
	r.Run(":9060")
}
```

```go
// MultipartForm is the parsed multipart form, including file uploads.
func (c *Context) MultipartForm() (*multipart.Form, error) {
	err := c.Request.ParseMultipartForm(c.engine.MaxMultipartMemory)
	return c.Request.MultipartForm, err
}

// SaveUploadedFile uploads the form file to specific dst.
func (c *Context) SaveUploadedFile(file *multipart.FileHeader, dst string) error {
	src, err := file.Open()
	if err != nil {
		return err
	}
	defer src.Close()

	if err = os.MkdirAll(filepath.Dir(dst), 0750); err != nil {
		return err
	}

	out, err := os.Create(dst)
	if err != nil {
		return err
	}
	defer out.Close()

	_, err = io.Copy(out, src)
	return err
}

```



### 多个文件上传

注意：选择文件时候要按住Ctrl同时选择多个文件

HTML前端代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>上传多个文件</title>
</head>
<body>
<form action="http://localhost:9090/upload" method="post" enctype="multipart/form-data">
    <input type="file" name="files" multiple="multiple" />
    <input type="submit" value="上传">
</form>
</body>
</html>
```

go后端代码：

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	engine := gin.Default()
	engine.LoadHTMLFiles("template/default/load.html")
	engine.GET("/upload", func(c *gin.Context) {
		c.HTML(http.StatusOK, "load.html", nil)
	})
	engine.POST("/upload", func(c *gin.Context) {
        //返回类型为*multipart.Form
		fp, err := c.MultipartForm()
		if err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return
		}
        //红色字体的files是在html文件里面的name值
		files := fp.File["files"]
		for _, file := range files {
			if err := c.SaveUploadedFile(file, file.Filename); err != nil {
				c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
				return
			} else {
				c.JSON(http.StatusOK, gin.H{
					"status": "ok",
				})
			}

		}
	})
	engine.Run(":9090")
}
```



## Gin请求重定向

### HTTP重定向

```go
package main
import (
	"github.com/gin-gonic/gin"
	"net/http"
)	
func main() {
	r := gin.Default()
	//外部重定向 可以通过Redirect跳转到外部页面
	//http.StatusMovedPermanently为状态码301 永久移动    请求的页面已永久跳转到新的url
	//第二个参数为跳转的外部地址
	r.GET("/t", func(c *gin.Context) {
		c.Redirect(http.StatusMovedPermanently, "http://www.baidu.com")
	})
    r.Run() 
}


```



### 路由重定向

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)
func main() {
	r := gin.Default()
	//内部重定向 通过c.Request.URL.Path 设置跳转的指定的路径
	//通过HandleContext函数
	r.GET("/move", func(c *gin.Context) {
		// 指定重定向的URL 通过HandleContext进行重定向到test2 页面显示json数据
		c.Request.URL.Path = "/test2"
		r.HandleContext(c)
	})
	r.GET("/test2", func(c *gin.Context) {
		c.JSON(http.StatusOK, gin.H{"hello": "world"})
	})
	r.Run()
}
```



## Gin路由和路由组

### 普通路由

```go
r.GET("/index", func(c *gin.Context) {...})
r.GET("/login", func(c *gin.Context) {...})
r.POST("/login", func(c *gin.Context) {...})
r.Delete("/login", func(c *gin.Context) {...})
//为没有配置处理函数的路由添加处理程序，默认情况下它返回404代码，下面的代码为没有匹配到路由的请求都返回views/404.html页面
r.NoRoute(func(c *gin.Context) {
    c.JSON(http.StatusNotFound,gin.H{
        "msg":"错误路由",
    })
})
```

匹配所有请求方法的Any

```go
package main

import (
	"github.com/gin-gonic/gin"
)
func main() {
	r := gin.Default()
	r.Any("/", func(c *gin.Context) {
		switch c.Request.Method {
		case "GET":
			c.JSON(200, gin.H{
			"message": "Hello World",
			})
			case "POST":
				c.JSON(200, gin.H{
				"message": "Hello World",
				}
		}
	})
	r.Run(":8000")
}

```



### 路由组

将拥有共同URL前缀的路由划分为一个路由组，通常用在划分业务逻辑或划分API版本时，基本原理是构造一个路由地址的前缀树。

```go
func main() {
	r := gin.Default()
	userGroup := r.Group("/user")
	{   
        //起到不用在path里面写/user了的作用
		userGroup.GET("/index", func(c *gin.Context) {...})
		userGroup.GET("/login", func(c *gin.Context) {...})
		userGroup.POST("/login", func(c *gin.Context) {...})

	}
	shopGroup := r.Group("/shop")
	{
		shopGroup.GET("/index", func(c *gin.Context) {...})
		shopGroup.GET("/cart", func(c *gin.Context) {...})
		shopGroup.POST("/checkout", func(c *gin.Context) {...})
	}
	r.Run()
}
```

路由组嵌套

```go
shopGroup := r.Group("/shop")
	{
		shopGroup.GET("/index", func(c *gin.Context) {...})
		shopGroup.GET("/cart", func(c *gin.Context) {...})
		shopGroup.POST("/checkout", func(c *gin.Context) {...})
		// 嵌套路由组
		xx := shopGroup.Group("xx")
		xx.GET("/oo", func(c *gin.Context) {...})
	}
```

# 中间件

Gin框架允许开发者在处理请求的过程中，加入用户自己的钩子（Hook）函数。这个钩子函数就叫中间件，中间件适合处理一些公共的业务逻辑，比如登录认证、权限校验、数据分页、记录日志、耗时统计等。

Gin中的中间件必须是一个`gin.HandlerFunc`类型

Gin中间件就是一个以 *gin.context 为参数的函数

```go
func (group *RouterGroup) GET(relativePath string, handlers ...HandlerFunc) IRoutes {
	return group.handle(http.MethodGet, relativePath, handlers)
}
 // HandlerFunc defines the handler used by gin middleware as return value.
type HandlerFunc func(*Context)
```

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
)

func shit(c *gin.Context) {
	fmt.Println("hello 我是第一个中间件")
}
func hello(c *gin.Context) {
	fmt.Println("hello 我是第二个中间件")
}

func main() {
	r := gin.Default()
	r.GET("/web", shit, hello, func(c *gin.Context) {
		c.String(200, "大傻叉是你吧")
		fmt.Println("你才是大傻叉")
	})
	r.Run(":8080")
}
/*
[GIN-debug] Listening and serving HTTP on :8080
hello 我是第一个中间件
hello 我是第二个中间件
你才是大傻叉
*/
```

c.Next()

```GO
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
)

func shit(c *gin.Context) {
	c.Next()
	fmt.Println("hello 我是第一个中间件")
}
func hello(c *gin.Context) {
	c.Next()
	fmt.Println("hello 我是第二个中间件")
}

func main() {
	r := gin.Default()
	r.GET("/web", shit, hello, func(c *gin.Context) {
		c.String(200, "大傻叉是你吧")
		fmt.Println("你才是大傻叉")
	})
	r.Run(":8080")
}
/*
你才是大傻叉
hello 我是第二个中间件
hello 我是第一个中间件
[GIN] 2024/05/30 - 17:46:29 | 200 |            0s |             ::1 | GET      "/web"
*/
```

c.Abort()

```GO
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
)

func shit(c *gin.Context) {
	c.Next()
	fmt.Println("hello 我是第一个中间件")
}
func hello(c *gin.Context) {
	c.Abort()
	fmt.Println("hello 我是第二个中间件")
}

func main() {
	r := gin.Default()
	r.GET("/web", shit, hello, func(c *gin.Context) {
		c.String(200, "大傻叉是你吧")
		fmt.Println("你才是大傻叉")
	})
	r.Run(":8080")
}
/*
[GIN-debug] Listening and serving HTTP on :8080
hello 我是第二个中间件
hello 我是第一个中间件
[GIN] 2024/05/30 - 17:48:23 | 200 |       265.6µs |             ::1 | GET      "/web"
*/
```

# 小清单项目

```go
package main

import (
    "github.com/gin-gonic/gin"
    "gorm.io/driver/mysql"
    "gorm.io/gorm"
    "net/http"
)

// 建一个model
type Todo struct {
    ID     int    `json:"id"`
    Title  string `json:"title"`
    Status bool   `json:"status"`
}

// 定义一个全局变量
var (
    DB *gorm.DB
)

func initMysql() (err error) {
    dsn := "root:123456@tcp(localhost:3306)/bubble?charset=utf8mb4&parseTime=True&loc=Local"
    //这里用= 而非：= 因为已经定义了一个全局变量，这里是去初始化这个全局变量
    DB, err = gorm.Open(mysql.Open(dsn))
    return
}
func main() {
    //创建数据库：cmd mysql里面去创建一下
    //连接数据库
    err := initMysql()
    if err != nil {
       panic(err)
    }
    //创建表，自动迁移
    DB.AutoMigrate(&Todo{})
    r := gin.Default()
    //找模板引用的静态文件
    r.Static("/static", "static")
    //找模板文件
    r.LoadHTMLGlob("templates/*")
    r.GET("/", func(c *gin.Context) {
       c.HTML(http.StatusOK, "index.html", nil)
    })
    //新建一个路由组
    v1Group := r.Group("v1")
    {
       //待办事项
       //添加
       v1Group.POST("/todo", func(c *gin.Context) {
          //前端页面填写待办事项，点击提交，会发请求到这里
          //1.从请求中把数据拿出来
          var todo Todo
          c.BindJSON(&todo)
          //2.存入数据库
          //err = DB.Create(&todo).Error
          //3.返回响应
          if err = DB.Create(&todo).Error; err != nil {
             c.JSON(http.StatusOK, gin.H{
                "error": err.Error()})
          } else {
             c.JSON(http.StatusOK, todo)
          }
       })
       //查看所有的待办事项
       v1Group.GET("/todo", func(c *gin.Context) {
          var todolist []Todo
          if err = DB.Find(&todolist).Error; err != nil {
             c.JSON(http.StatusOK, gin.H{
                "error": err.Error()})
          } else {
             c.JSON(http.StatusOK, todolist)
          }
       })
       //查看某一个待办事项
       v1Group.GET("/todo/:id", func(c *gin.Context) {})
       //修改某一个待办事项
       v1Group.PUT("/todo/:id", func(c *gin.Context) {
          id, ok := c.Params.Get("id")
          if !ok {
             c.JSON(http.StatusOK, gin.H{"error": "id is required"})
          }
          var todo Todo
          if err = DB.Where("id=?", id).First(&todo).Error; err != nil {
             c.JSON(http.StatusOK, gin.H{"error": err.Error()})
             return
          }
          c.BindJSON(&todo)
          if err = DB.Save(&todo).Error; err != nil {
             c.JSON(http.StatusOK, gin.H{"error": err.Error()})
          } else {
             c.JSON(http.StatusOK, todo)
          }
       })
       //删除某一个待办事项
       v1Group.DELETE("/todo/:id", func(c *gin.Context) {
          id, ok := c.Params.Get("id")
          if !ok {
             c.JSON(http.StatusOK, gin.H{"error": "id is required"})
             return
          }
          if err = DB.Where("id=?", id).Delete(Todo{}).Error; err != nil {
             c.JSON(http.StatusOK, gin.H{"error": err.Error()})
          } else {
             c.JSON(http.StatusOK, gin.H{"message": "已删除"})
          }
       })
    }
    r.Run(":8080")

}
```

企业级项目结构拆分后

```go

```

