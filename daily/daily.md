## 20231204

1）mybatis里使用mapper接口时，方法可以被重载，但映射文件里的id必须唯一。通常能够重载的方式为：

- 一个无参方法，一个有参方法
- 多个有参方法，需要参数数量相同，使用@param注解，否则少参的那个报错

mapper接口的工作原理是 JDK 动态代理，MyBatis 运行时会使用 JDK 动态代理为 mapper 接口生成代理 proxy 对象，代理对象 proxy 会拦截接口方法，转而执行 MappedStatement 所代表的 sql，然后将 sql 执行结果返回。

2）mybatis的分页和原理。

- MyBatis 使用 RowBounds 对象进行分页，它是针对 ResultSet 结果集执行的内存分页，而非物理分页；
- 可以在 sql 内直接书写带有物理分页的参数来完成物理分页功能；
- 使用分页插件来完成物理分页。分页插件的基本原理是使用 MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 sql，然后重写 sql，根据 dialect 方言，添加对应的物理分页语句和物理分页参数。

逻辑分页主要用于数据量不大、数据稳定的场合，物理分页主要用于数据量较大、更新频繁的场合。

3）JDK9以后，String的实现内部改为使用**byte数组（byte[]）**。这样做的主要原因是为了**节省内存空间**，因为对于大量的拉丁文系列字符（如英文、数字、常见的标点符号等），使用byte数组存储比使用char数组可以节省一半的空间。

4）synchronized的偏向锁已经被废弃了。真实情况中，偏向锁**并不总能带来预期的性能优势**，相反地，在某些情况下（多核处理器环境），偏向锁的撤销需要进入**全局安全点**（即safepoint，虚拟机将所有的线程暂停执行），会带来比较长的停顿时间。偏向锁想法是好的，但是增加了JVM的**复杂性**，同时也并没有为所有应用都带来性能提升。因此，在JDK15中，偏向锁被默认关闭，在JDK18中，偏向锁已经被彻底废弃（无法通过命令行打开）。

5）G1推出后，分代回收的策略也发生了变化。DK7已经引入了G1垃圾收集器，在JDK9中被设置为默认的垃圾收集器。在G1中，没有严格的年轻代和老年代的划分，而是分为多个大小相同的独立区域，每个区域在不同的时间点可能会扮演不同的角色。

6）泛型擦除。Java的泛型实现使用了类型擦除，这意味着泛型信息只存在于编译时，而在运行时则被擦除，替换为它的限定类型（如果没有明确指定，那就是Object）。

参考：[javaguide](https://javaguide.cn/system-design/framework/mybatis/mybatis-interview.html)、[你的八股文可能过时了](https://mp.weixin.qq.com/s/i4KK7SWuGXcfTOAsxSUieA)

## 20231205

1）defineProps() 和 defineEmits()。可以更方便地使用这两个api

```JavaScript
<script setup>
const props = defineProps({
  foo: String
})

const emit = defineEmits(['change', 'delete'])
// setup 代码
</script>
```

或者

```javascript
const props = defineProps<{
  foo: string
  bar?: number
}>()

const emit = defineEmits<{
  (e: 'change', id: number): void
  (e: 'update', value: string): void
}>()

// 3.3+：另一种更简洁的语法
const emit = defineEmits<{
  change: [id: number] // 具名元组语法
  update: [value: string]
}>()
```



参考：[vue](https://cn.vuejs.org/api/sfc-script-setup.html#defineprops-defineemits)

## 20231206

1）大文件**分片上传和断点续传**。

https://cloud.tencent.com/developer/article/1813858

2）var、let。var可以跨块的访问（比如if和for语句），但是不能跨函数，定义在函数的变量外部不能访问，且var可以被重新声明和修改。let只能在块级、函数级内部作用域访问，不能被再次声明。

**变量提升**。

- 在es6中，**变量**和**函数**的声明会在物理层面移动到最前面，实际是编译时放入内存里。let和 const 定义的*变量*是会*提升*的，只是它们*提升*的时候不会进行默认初始化，使得它们无法被访问（因为这些*变量*在暂时性死区里）。
- 变量被提升后，被赋值为undefined。

```javascript
// 输出undefined
// 函数内部的变量声明会被提升至函数作用域的顶端
function getNum() {
  console.log(num) 
  var num = 1  
}
getNum()
// 提升后等价于
function getNum() {
  var num 
  console.log(num) 
  num = 1  
}
getNum()

```

3）数据脱敏

- hutool的DesensitizedUtil工具类。
- [jackson通过注解脱敏](https://javaguide.cn/system-design/security/data-desensitization.html#hutool)。



参考：[javaguide](https://javaguide.cn/system-design/security/data-desensitization.html#hutool)、[浅谈 JavaScript 变量提升](https://juejin.cn/post/7007224479218663455)



## 20231207

1）url编码。通常对url中的参数进行编码，以消除歧义。例如"name1=value1”,其中value1的值是“va&lu=e1”字符串，那么实际在传输过程中就会变成这样“name1=va&lu=e1”。

参考：[什么情况下需要urlencode编码](https://www.cnblogs.com/yingsong/p/8884692.html)、[Urlencode踩坑日记](https://zhuanlan.zhihu.com/p/130695874?utm_id=0)



## 20231208

1）document.addEventListener()方法用于向文档添加事件句柄，使用 [document.removeEventListener()](https://www.runoob.com/jsref/met-document-removeeventlistener.html) 方法来移除 addEventListener() 方法添加的事件句柄，使用 [*element.*addEventListener()](https://www.runoob.com/jsref/met-element-addeventlistener.html) 方法为指定元素添加事件句柄。

语法：

```javascript

document.addEventListener(event, function, useCapture)
```

参数：

| 参数         | 描述                                                         |
| :----------- | :----------------------------------------------------------- |
| *event*      | 必需。描述事件名称的字符串。  **注意：** 不要使用 "on" 前缀。例如，使用 "click" 来取代 "onclick"。  **提示：** 所有 HTML DOM 事件，可以查看我们完整的 [HTML DOM Event 对象参考手册](https://www.runoob.com/jsref/dom-obj-event.html)。 |
| *function*   | 必需。描述了事件触发后执行的函数。  当事件触发时，事件对象会作为第一个参数传入函数。 事件对象的类型取决于特定的事件。例如， "click" 事件属于 MouseEvent(鼠标事件) 对象。 |
| *useCapture* | 可选。布尔值，指定事件是否 在捕获或冒泡阶段执行。  可能值：true - 事件句柄在捕获阶段执行false- 默认。事件句柄在冒泡阶段执行 |



参考：[addEventListener](https://www.runoob.com/jsref/met-document-addeventlistener.html)



## 20231210

1）vue.nextTick()。Vue.nextTick用于在DOM更新完成后执行延迟回调，在修改数据之后使用$nextTick，则可以在回调中获取更新后的DOM。

总的来说，Vue.nextTick的主要应用的场景及原因如下：

- 解决dom没有渲染的时候，无法做一些操作，我们想在dom完成后执行的。
- 处理数据动态变化后，DOM还未及时更新的问题

## 20231211

1）vue3里面，引入组件和挂载方式与vue2不同：

```javascript
import {createApp} from 'vue'
import App from './App.vue'
import uploader from 'vue-simple-uploader'

const app = createApp(App)

app.use(uploader)

app.mount('#app')
```

2）断点续传（上传）。

**收集问题：**

- 页面刷新/崩溃、网络重连等怎么续传(不重复传)
  - 上传时检查服务器分片上传信息，返回未上传的分片继续上传。重传也同样。

- 怎么自动恢复上传
  - 上传失败后进行提示，手动点击重新上传进行恢复。

- chunk会不会出现损坏的情况，损坏了怎么办。
  - chunk全部上传后，前端发起合并请求，后端检查合并文件md5是否正确。
  
- 记录chunk上传状态的方式
  - 生成一个conf文件记录chunk上传状态：设置文件的长度为chunkSize，每次有chunk写入后，都在文件的chunkNumber偏移量处写入`Byte.MaxValue`（127，可能是直接符号运算更快？），检查哪些chunk没传或者是否传完，就把文件读到byte数组`FileUtils.readFileToByteArray(confFile)`，如果下标处不为`Byte.MaxValue`，就没传过。思路来源：[file-upload](https://gitee.com/Analyzer/vip-file-upload)
  - 使用数据库表记录分片信息，文件上传成功后，通过文件md5删除分片信息。[大文件分片上传及下载、断点续传、秒传](https://zhuanlan.zhihu.com/p/611352814)

- 前端分片是并发请求，怎么在请求全部成功后，发起合并请求

  - `Promise.all(iterable)`: 接受一个可迭代对象（比如数	组），返回一个 Promise，只有当所有 Promise 都成功时才会成功，一旦有一个 Promise 失败就立即失败。

    [一次看明白大文件上传](https://blog.csdn.net/qq_43220213/article/details/130120575)
  
  ```javascript
  // 模拟上传分片的函数
  function uploadChunk(chunk) {
    return new Promise((resolve, reject) => {
      // 模拟异步上传分片的操作
      setTimeout(() => {
        // 模拟上传成功，返回分片信息
        const chunkInfo = {
          chunkId: chunk,
          success: true,
        };
        resolve(chunkInfo);
        // 模拟上传失败的情况
        // reject(new Error(`Failed to upload chunk ${chunk}`));
      }, Math.random() * 1000); // 使用随机延迟模拟异步操作
    });
  }
  
  // 模拟合并请求的函数
  function mergeChunks(chunkInfos) {
    return new Promise((resolve, reject) => {
      // 模拟异步合并操作
      setTimeout(() => {
        // 判断所有分片是否成功上传
        const allChunksUploaded = chunkInfos.every((chunk) => chunk.success);
  
        if (allChunksUploaded) {
          // 模拟合并成功
          resolve("Merge successful");
        } else {
          // 模拟合并失败
          reject(new Error("Failed to merge chunks"));
        }
      }, Math.random() * 1000);
    });
  }
  
  // 生成上传分片的任务数组
  const chunkTasks = Array.from({ length: 5 }, (_, index) => uploadChunk(index + 1));
  
  // 使用Promise.all并发上传所有分片
  Promise.all(chunkTasks)
    .then((chunkInfos) => {
      // 所有分片上传成功，发起合并请求
      console.log("All chunks uploaded successfully:", chunkInfos);
      return mergeChunks(chunkInfos);
    })
    .then((mergeResult) => {
      // 合并请求成功
      console.log("Merge successful:", mergeResult);
    })
    .catch((error) => {
      // 任何一个步骤出现错误都会进入这里
      console.error("Error:", error.message);
    });
  
  ```
  
  

**前端上传组件**：

[vue-simple-uploader](https://github.com/simple-uploader/vue-uploader/blob/master/README_zh-CN.md)

- 指定上传分片。检测 `preprocess` 的 `chunk.preprocessFinished()` 来控制哪一片需要上传。

**实现方式**：

- 使用RandomAccessFile完成从指定位置写入数据。前端上传文件后，检查文件md5，看有没有上传过，传过的话前端直接秒传；上传过一部分的话，返回还有哪些需要上传；没传过的话，从头上传。前端分片上传文件，后端收到分片后seek()到临时文件的偏移处写入（chunkSize*chunkNumber），**并记录这个分片已经上传**；然后检查分片是否全上传了，返回给前端，如果是，前端发送合并请求；上传失败了，重传；上传成功了，继续传其他分片。

- 使用MappedByteBuffer。

**使用redis存储分片：**

[断点续传](https://juejin.cn/post/7222910781921837093#heading-13)

## 20231212

1）浏览器请求最大并发数。

| 版本                           | HTTP 1.0 服务器（宽带连接） | HTTP 1.1 服务器（宽带连接） | HTTP 1.0 服务器（拨号连接） | HTTP 1.1 服务器（拨号连接） |
| ------------------------------ | --------------------------- | --------------------------- | --------------------------- | --------------------------- |
| Internet Explorer 7 和早期版本 | 4                           | 2                           | 4                           | 2                           |
| Internet Explorer 8            | 6                           | 6                           | 4                           | 2                           |
| Internet Explorer 9            | 10                          | 10                          | ?                           | ?                           |
| Internet Explorer 10           | 6                           | 6                           | ?                           | ?                           |
| Internet Explorer 11           | 6                           | 6                           | ?                           | ?                           |
| chrome、firefox                | 6                           | 6                           | ?                           | ?                           |



## 20231213

1）redis存储二进制，可以先base64编码后再存储。

2）validation。javax.validation即将被jakarta.validation替代，但在Jakarta.validation需要springboot3、spring6以上，springboot2.x、spring5.x还是用javax.validation。

3）multipartfile。multipartfile如果封装在传输对象里，拿来接收前端参数，加上@Requestbody会报错；可以不加这个注解接收参数，或者改为`@ModelAttribute`。

例如：

```JAVA
public boolean uploadChunk(@ModelAttribute @Validated UploadVO uploadVO) {
	// dothing 
}
```

VO：

```JAVA
@Getter
@Setter
public class UploadVO {
    @NotNull
    @Max(5)
    private Integer number;
    private MultipartFile file;
}
```

**@RequestBody**是解析json字符串为map、java bean，因此支持的`contentType`是`"application/json;charset=UTF-8"`。

4）join。

![img](https://img2020.cnblogs.com/blog/162475/202003/162475-20200312163615988-926264670.jpg)

## 20231215

1）vue-cli代理可以处理开发环境跨域问题；但在生产阶段没有vue-cli脚手架，一般放到nginx里反向代理。

**开发环境：**

```javascript
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,
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

2）vue3里未被使用的变量报错，把它取消掉：在package.json里找到rules，添加如下

```json
    "rules": {
      "no-unused-vars": "off"
    }
```

## 20231218

1）nginx。

**windows篇：**

启动服务

```BASH
start nginx
```

测试配置文件

```BASH
nginx -t -c /nginx-1.15.2/conf/nginx.conf
```

重新加载并启动

```BASH
nginx -s reload
```

停止

```BASH
nginx -s stop
```

正常关闭

```BASH
nginx -s quit
```

**配置**

nginx.conf

```text
# 每个server对应一个主机
server {
	listen	80;
	# 主机、域名
	server_name localhost;
	location / {
		# 根路径
		root html;
		index index.html index.htm;
		# 例如输入baidu.com/test
		try_files $uri $uri/ /index.html;
	}
}
```



**问题**

浏览器访问项目根目录，想跳转到指定页面，但是nginx报403



2）vue。

- `@views` 是 `src/views` 的路径别名，`@cp` 是 `src/components` 的路径别名。

- `import.meta.env.BASE_URL` 是由 Vite 提供的环境变量；@vue/cli 创建的项目因为是基于 Webpack ，所以使用的是 `process.env.BASE_URL` 。



## 20231220

1）No match found for location with path "xxx"。打开一看是router里name重复了。

router.js：

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
    }, {
        path: '/admin/upload',
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

2）vue不同环境打包。npm run build:prod

process.env.NODE_ENV



3）vue-router，设置404页面。

router.js：

```javascript
import {createRouter, createWebHistory} from 'vue-router'

const routes = [
    // ...省略其他的
    {
        path: "/404",
        name: '404',
        component: () => import('@/views/NotFound.vue')
    }
]

const router = createRouter({
    history: createWebHistory(process.env.BASE_URL),
    routes,
})

export default router
```

main.js：

```javascript
import {createApp} from 'vue'
import App from './App.vue'
import router from "@/router/router";

const app = createApp(App)

router.beforeEach((to, from, next) => {
    if (!to.matched.length) {
        next('/404');
    } else {
        next();
    }
});

app.use(router)
app.mount('#app')
```

## 20240103

1）使用`.sync`修饰符，它会自动监听子组件的更新事件，这样父组件中的`parentData`会随着子组件的变化而更新。`.sync`实际是一个语法糖。

```html
<template>
  <div>
    <p>{{ parentData }}</p>
    <ChildComponent :parentProp.sync="parentData"></ChildComponent>
  </div>
</template>
```

// 子组件内

```html
<script>
    this.$emit('update:parentProp', value);
</script>
```

## 20240105

1）uniapp里，子组件的 `onLoad` 生命周期并不会执行，它是页面的生命周期，在页面加载时进行初始化工作。在组件被创建时执行一些初始化操作，可以使用组件的生命周期比如 `beforeCreate` 和 `created`。

2）updated生命周期会在数据发生变化、导致重新渲染时被调用。

## 20210110

1）vue页面间通信。

- 父子间，`props`和`$emit`
- 通过vuex状态管理。用于大型应用状态管理。
- `EventBus`，简单的通信组件。

2）js与ts的重载。js 并没有直接支持重载，而是通过参数的个数和类型进行判断和处理。ts可以使用函数重载来实现不同参数类型和个数的函数。

js：

```javascript
function exampleFunction() {
  if (arguments.length === 1 && typeof arguments[0] === 'number') {
    // 处理只有一个参数且是数字的情况
    console.log('Function with one numeric argument:', arguments[0]);
  } else if (arguments.length === 2 && typeof arguments[0] === 'string' && typeof arguments[1] === 'boolean') {
    // 处理有两个参数，第一个是字符串，第二个是布尔值的情况
    console.log('Function with a string and a boolean argument:', arguments[0], arguments[1]);
  } else {
    // 处理其他情况或抛出错误
    console.log('Invalid arguments');
  }
}

// 调用函数
exampleFunction(42);                            // 调用第一个情况
exampleFunction('Hello', true);                // 调用第二个情况
exampleFunction('Invalid', false, 'Extra');    // 调用第三个情况

```

ts：

```typescript
// 函数重载的定义
function exampleFunction(x: number): void;
function exampleFunction(s: string, b: boolean): void;

// 实际函数实现
function exampleFunction(x: number | string, b?: boolean): void {
  if (typeof x === 'number') {
    console.log('Function with one numeric argument:', x);
  } else if (typeof x === 'string' && typeof b === 'boolean') {
    console.log('Function with a string and a boolean argument:', x, b);
  } else {
    console.log('Invalid arguments');
  }
}

// 调用函数
exampleFunction(42);                            // 调用第一个情况
exampleFunction('Hello', true);                // 调用第二个情况
exampleFunction('Invalid', false, 'Extra');    // 错误，不匹配的参数

```

