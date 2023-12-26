# Vue

## 404路由

main.js

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

router.js

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
    },
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

## 模式和环境变量

[模式和环境变量](https://cli.vuejs.org/zh/guide/mode-and-env.html)

[vue3.0 多环境配置](https://www.jianshu.com/p/0c4ca3adb14d)

自定义stage模式后，如果以默认模式启动，stage的环境变量是读不到的。

**process.env.NODE_ENV**

默认有development、test、production三种模式。可以通过在package.json里指定配置：

```json
{
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "serve:test": "vue-cli-service serve --mode test",
    "build:test": "vue-cli-service build --mode test",
    "serve:production": "vue-cli-service serve --mode production",
    "build:production": "vue-cli-service build --mode production",
    "lint": "vue-cli-service lint"
  }
} 
```

通过`console.log(process.env.NODE_ENV)`，输出对应环境；也可以在脚本里判断当前环境做一些判断。但通过在script里--mode的方式，只能识别test、和production：如果没指定mode、指定mode为prod等，输出值都会得到`development`。

## 解构赋值语法

[解构赋值语法](https://www.cnblogs.com/mydyxy/p/17454391.html)

## 处理 `v-model` 修饰符

[处理 v-model修饰符](https://cn.vuejs.org/guide/components/v-model.html#component-v-model)

## 动态参数 

[动态参数](https://cn.vuejs.org/guide/essentials/template-syntax.html#dynamic-arguments)