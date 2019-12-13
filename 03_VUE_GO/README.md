Vue+Go用户登录实践

系统环境版本说明：
* nodejs:  v11.10.1
* npm:  6.13.1
* go:   go1.13.5 darwin/amd64
* vue:  @vue/cli 4.0.5

名词解释：
* vue-router:是vue.js官方的路由插件
* Vuex:是一个专为 Vue.js 应用程序开发的状态管理模式。
* SASS:SASS（Syntactically Awesome Stylesheet）是一个CSS预处理器，有助于减少CSS的重复，节省时间。它是更稳定和强大的CSS扩展语言，描述文档的样式干净和结构。
* Babel: Babel 是一个 JavaScript 编译器
* ESLint: ESLint 是一个插件化并且可配置的 JavaScript 语法规则和代码风格的检查工具。





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
 # go get -u github.com/gin-gonic/gin
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
    r.Run(":8001") // listen and serve on 0.0.0.0:8001
}
```

运行


```
 # go run main.go
```


```
http://localhost:8001/abc
```
![](/03_VUE_GO/images/01@2x.png)

2、编写后端用户验证接口


```
package main

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

type LoginForm struct {
    User     string `form:"user" binding:"required"`
    Password string `form:"password" binding:"required"`
}

func main() {
    router := gin.Default()
    router.POST("/api/user/login", func(c *gin.Context) {
        var form LoginForm
        if c.ShouldBind(&form) == nil {
            if form.User == "user" && form.Password == "password" {
                c.JSON(http.StatusOK, gin.H{"status" :1,"message":"身份验证成功"})
            } else {
                c.JSON(http.StatusOK, gin.H{"status" :0,"message":"身份验证失败"})
            }
        }
    })
    router.Run(":8001")
}
```
运行程序

```
 # go run main.go
```

使用PostMan工具进行调试

![](/03_VUE_GO/images/02@2x.png)

![](/03_VUE_GO/images/03@2x.png)



3、搭建Vue框架
* 创建新项目

```
 # vue create demo01
```

* 运行Vue应用

```
 # rpm run serve
```

* 浏览器访问Web
```
http://localhost:8080/
```

* 安装element UI (https://element.eleme.cn/#/zh-CN)


```
 # vue add element
```


```
✔  Successfully installed plugin: vue-cli-plugin-element

? How do you want to import Element? Fully import
? Do you wish to overwrite Element's SCSS variables? No
? Choose the locale you want to load zh-CN

✔  Successfully invoked generator for plugin: vue-cli-plugin-element
   The following files have been updated / added:

     src/plugins/element.js
     package-lock.json
     package.json
     src/App.vue
     src/main.js
     
```

4、编写Vue用户登录功能
![](/03_VUE_GO/images/05@2x.png)

在APP.vue中使用router-view
```
<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

```


router/index.js增加路由

```
{
    path: "/login",
    name: "login",
    component: () =>
      import(/* webpackChunkName: "about" */ "../views/Login.vue")
  },
```

在views中增加Login.vue文件


```
<template>
  <div class="login-container">
    <el-form ref="loginForm" :model="loginForm" :rules="loginRules" class="login-form" auto-complete="on" label-position="left">

      <div class="title-container">
        <h3 class="title">后台管理系统</h3>
      </div>

      <el-form-item prop="username">
        <span class="svg-container">
          
        </span>
        <el-input
          ref="username"
          v-model="loginForm.username"
          placeholder="用户名"
          name="username"
          type="text"
          tabindex="1"
          auto-complete="on"
        />
      </el-form-item>

      <el-tooltip v-model="capsTooltip" content="Caps lock is On" placement="right" manual>
        <el-form-item prop="password">
          <span class="svg-container">
            
          </span>
          <el-input
            :key="passwordType"
            ref="password"
            v-model="loginForm.password"
            :type="passwordType"
            placeholder="密码"
            name="password"
            tabindex="2"
            auto-complete="on"
            @keyup.native="checkCapslock"
            @blur="capsTooltip = false"
            @keyup.enter.native="handleLogin"
          />
         
        </el-form-item>
      </el-tooltip>
      <el-button :loading="loading" type="primary" style="width:100%;margin-bottom:30px;" @click.native.prevent="handleLogin">登录</el-button>

    </el-form>

  </div>
</template>

<script>
import loginApi from '@/api/login'

export default {
  name: 'Login',
  data () {
    const validateUsername = (rule, value, callback) => {
      if (value.length < 5) {
        callback(new Error('用户名不能少于5个字符'))
      } else {
        callback()
      }
    }
    const validatePassword = (rule, value, callback) => {
      if (value.length < 5) {
        callback(new Error('密码不能少于5个字符'))
      } else {
        callback()
      }
    }
    return {
      loginForm: {
        username: 'admin',
        password: '123456'
      },
      loginRules: {
        username: [{ required: true, trigger: 'blur', validator: validateUsername }],
        password: [{ required: true, trigger: 'blur', validator: validatePassword }]
      },
      passwordType: 'password',
      capsTooltip: false,
      loading: false,
      showDialog: false
    }
  },
  created () {
    // window.addEventListener('storage', this.afterQRScan)
  },
  mounted () {
    if (this.loginForm.username === '') {
      this.$refs.username.focus()
    } else if (this.loginForm.password === '') {
      this.$refs.password.focus()
    }
  },
  destroyed () {
    // window.removeEventListener('storage', this.afterQRScan)
  },
  methods: {
    handleLogin () {
      let _this = this
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true
          loginApi.login(this.loginForm).then(function (result) {
            console.info(result)
            if (result && result.statusText === "OK") {
              _this.$router.push({ path: '/' })
            } else {
              _this.loading = false
              _this.$message({
                message: result.statusText,
                type: 'error'
              })
            }
          }).catch(function () {
            _this.loading = false
          })
        } else {
          return false
        }
      })
    },
  }
}
</script>


增加login接口，新建api/login.js
```
import { post } from '@/utils/request'

export default {
  login: query => post(`/api/user/login`, query)
}
```

```

修改vue.config.js文件，指定api的proxy


```
'use strict'
const path = require('path')

function resolve (dir) {
  return path.join(__dirname, dir)
}

module.exports = {
  devServer: {
    open: true,
    host: 'localhost',
    port: 9999,
    https: false,
    hotOnly: false,
    proxy: {
      '/api': {
        target: 'http://localhost:8001',
        changeOrigin: true
      }
    }
  },
}

```

在src目录中新建utils目录，新建request.js文件


```
import axios from 'axios'
import vue from 'vue'

const request = function (query) {
  return axios.request(query)
    .then(res => {
      console.info(res)
      return Promise.resolve(res)
    })
    .catch(e => {
      vue.prototype.$message.error(e.message)
      return Promise.reject(e.message)
    })
}


const post = function (url, params) {
  const query = {
    baseURL: process.env.VUE_APP_URL,
    url: url,
    method: 'post',
    withCredentials: true,
    timeout: 30000,
    data: params,
    headers: { 'Content-Type': 'application/json', 'request-ajax': true }
  }
  return request(query)
}

const postWithLoadTip = function (url, params) {
  const query = {
    baseURL: process.env.VUE_APP_URL,
    url: url,
    method: 'post',
    withCredentials: true,
    timeout: 30000,
    data: params,
    headers: { 'Content-Type': 'application/json', 'request-ajax': true }
  }
  return request( query)
}

export {
  post,
  postWithLoadTip
}

```


附加内容

在Web后端增加静态资源（可跳过）


```
package main

import (
    "github.com/gin-gonic/gin"
    "net/http"
)

type LoginForm struct {
    User     string `form:"user" binding:"required"`
    Password string `form:"password" binding:"required"`
}

func main() {
    router := gin.Default()
    router.POST("/api/user/login", func(c *gin.Context) {
        var form LoginForm
        if c.ShouldBind(&form) == nil {
            if form.User == "user" && form.Password == "password" {
                c.JSON(http.StatusOK, gin.H{"status" :1,"message":"身份验证成功"})
            } else {
                c.JSON(http.StatusOK, gin.H{"status" :0,"message":"身份验证失败"})
            }
        }
    })
    router.Static("/assets", "./assets")
    router.Run(":8001")
}

```



将静态文件放到assets目录下
打开浏览器，访问静态资源
![](/03_VUE_GO/images/04@2x.png)

使用gorm访问数据库（可跳过）







参考：
* vue cli 4.0.5 的使用 https://www.cnblogs.com/qinyuanchun/p/11821918.html
