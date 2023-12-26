## 命令

```BASH
## linux环境

# nginx！启动！
nginx
# 测试配置文件内容是否正确
nginx -t -c nginx.conf
# 重新加载、启动
nginx -s reload
# 直接停止
nginx -s stop
# 正常退出
nginx -s quit
```

## 配置文件

[Nginx配置文件详解](https://www.cnblogs.com/54chensongxia/p/12938929.html)

### location

[一份简单够用的 Nginx Location 配置讲解](https://github.com/mqyqingfeng/Blog/issues/242)

### try_files

[How can I make this try_files directive work?](https://stackoverflow.com/questions/17798457/how-can-i-make-this-try-files-directive-work)

## vue部署

### 1. router.js

路由配置

```javascript
import {createRouter, createWebHistory} from 'vue-router'

const routes = [
    {
        path: '/',
        redirect: '/upload'
    }, {
        path: '/upload',
        name: 'FileUpload',
        component: () => import('@/views/FileUpload.vue')
    }
]

const router = createRouter({
    history: createWebHistory(process.env.BASE_URL),
    routes,
})

export default router
```

### 2. vue.config.js

配置文件

```javascript
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,
  // 如果配置有二级或以上的路由，不要使用'./'
  // 如果部署在域名根目录下，就写'/',如果部署在子目录下，则前后加上'/',例如'/admin/'
  publicPath: '/',
  // 开发环境下，跨域代理  
  devServer: {
    proxy: {
      // http://localhost:8080/api/xxx -> http://localhost:9090/api/xxx
      '/api': {
        target: 'http://localhost:9090'
      }
    }
  }
})
```

### 3. 打包、部署

`npm build`打包，把打包的`dist`目录里的内容，拷贝到nginx的根目录下，默认是html目录。

### 4. 修改conf

修改`conf/nginx.conf`文件（这是部署的最简配置）

```
    server {
        listen       8077;
        server_name  localhost;
        location / {
	        try_files $uri $uri/ /index.html;
        }
    }
```

### 5. 启动

```bash
nginx
```

## 记录问题

- rewrite的flag指定permanent后，会被浏览器永久重定向（301），可以清除浏览器的浏览数据恢复。建议使用rewrite redirect（302），临时定向

## 正则复健

`^` ：字符串开头。例如`^2a`，需要字符串以2a开头

`$` ：字符串结尾。例如`2a$`，需要字符串已2a结尾。
