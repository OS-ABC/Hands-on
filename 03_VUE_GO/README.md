Vue+Go用户登录实践

系统环境版本说明：
* nodejs:  v11.10.1
* npm:  6.13.1
* go:   go1.13.5 darwin/amd64
* vue:  @vue/cli 4.0.5

名词解释：
* vue-router是vue.js官方的路由插件
* Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。
* SASS： SASS（Syntactically Awesome Stylesheet）是一个CSS预处理器，有助于减少CSS的重复，节省时间。它是更稳定和强大的CSS扩展语言，描述文档的样式干净和结构。
* Babel： Babel 是一个 JavaScript 编译器
* ESLint： ESLint 是一个插件化并且可配置的 JavaScript 语法规则和代码风格的检查工具。





1、Gin框架

* Go Go（又称 Golang）是 Google 开发的一种静态强类型、编译型语言。
* Go 语言语法与 C 相近，但功能上有：内存安全，GC（垃圾回收），结构形态及 CSP-style 并发计算。
* 与C++相比，Go并不包括如枚举、异常处理、继承、泛型、断言、虚函数等功能，但增加了 切片(Slice) 型、并发、管道、垃圾回收、接口（Interface）等特性的语言级支持。Go 2.0版本将支持泛型，对于断言的存在，则持负面态度，同时也为自己不提供类型继承来辩护。
* Gin 是一个 go 写的 web 框架，具有高性能的优点。官方地址：https://github.com/gin-gonic/gin


```
 查看Go版本
 #go version
  go version go1.13.5 darwin/amd64

```

* 安装gin

```
go get -u github.com/gin-gonic/gin
```

示例代码


```
package main

import "github.com/gin-gonic/gin"

func main() {
    r := gin.Default()
    r.GET("/abc", func(c *gin.Context) {
        c.JSON(200, gin.H{
            "message": "Hello ABC!",
        })
    })
    r.Run() // listen and serve on 0.0.0.0:8080
}
```

运行


```
go run main.go
```


```
http://localhost:8080/ping
```


2、编写后端用户验证接口



3、搭建Vue框架
* 创建新项目


```
vue create demo01
```

* 运行Vue应用


```
rpm run serve
```

* 浏览器访问Web
```
http://localhost:8080/
```









4、编写Vue用户登录功能



5、程序优化

