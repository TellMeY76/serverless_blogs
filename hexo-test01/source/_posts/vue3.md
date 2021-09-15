---
title: vue3入门笔记
tags: [Vue3]
categories: learning
toc: true
mathjax: true
---
vue3入门学习笔记
 <!-- more -->

 #### Hello，Vite

 Vite是vue作者开发的一款意图取代webpack的工具，实现原理是利用ES6的import发送请求去加载文件的特性，去拦截这些请求，做预编译，省去了webpack冗长的打包时间

 Vite生成的脚手架项目的目录结构与Vue CLI生成的项目目录结构很类似，确实是这样的，而且开发方式也基本相同。不过Vite项目的默认配置文件是vite.config.js，而不是vue.config.js。

* 优点:
1. 因为没有打包工作要做，所以本地服务器冷启动非常快。
2. 代码是按需编译的，因此只有在当前页面上实际导入的代码才会编译。我们不必等到整个应用程序打包后才开始开发，这对于有几十个页面的应用程序来说是一个巨大的不同

一个字概括，就是快

 #### 实现双向绑定的原理不同了

 在vue2中，实现数据双向绑定主要是基于ES5中的Object.defineProperty()api实现的。 vue2会对data属性中定义的数据进行递归将所有属性都转化为 getter/setter 的形式，因此在对data中的某个值赋值时，就会触发 setter，那么就能监听到了数据变化。
 也正是由于这种实现方式，vue2的双向绑定中会存在一些限制，比如无法原生的对数组进行响应式监听，而为了实现对于深度嵌套的数据，就需要使用递归，进而消耗大量性能

 *  对数组进行递归遍历

 ```
  observeArray (items: Array<any>) {
    for (let i = 0, l = items.length; i < l; i++) {
      observe(items[i])  // observe 功能为监测数据的变化
    }
  }
```


 在vue3则是使用ES6 的 Proxy api 对数据进行代理，具有以下优点(可能没有列全):
 1. Proxy支持监听原生数组和对象

 2. Proxy的获取数据，只会递归到需要获取的层级，不会继续递归

 3. Proxy 返回的是一个新对象,我们可以只操作新的对象达到目的,而 Object.defineProperty 只能遍历对象属性直接修改；

 4. Proxy 有多达 13 种拦截方法,不限于 apply、ownKeys、deleteProperty、has 等等是 Object.defineProperty 不具备的；

#### 听说我长得很像React Hooks, Composition api闪亮登场
 在vue2中，使用 (data、computed、methods、watch) 组件选项来组织逻辑通常都很有效。然而，当我们的组件开始变得更大时，逻辑关注点的列表也会增长。尤其对于那些一开始没有编写这些组件的人来说，这会导致组件难以阅读和理解。

 而vue3为了解决这个问题，进而引入了Composition api。Composition API 是围绕一个新的组件选项 setup 而创建的，它只是一个函数，向模板返回属性和函数。就是这样。我们在这里声明所有的响应式属性、计算属性、watchers和生命周期钩子，然后返回它们，这样它们就可以在模板中使用。因此我们可以使用setup()函数将逻辑关联的代码单独剥离出来，同时也增加了这段逻辑代码的可复用性。
 

 #### 生命周期不同了
 destroyed 生命周期被重命名为 unmounted

 beforeDestroy 生命周期被重命名为 beforeUnmount

 因为 setup 是围绕 beforeCreate 和 created 生命周期钩子运行的，所以不需要显式地定义它们。换句话说，在这些钩子中编写的任何代码都应该直接在 setup 函数中编写。

 你可以通过在生命周期钩子前面加上 “on” 来访问组件的生命周期钩子。

 ```
import {onMounted} from 'vue'

 export default {
  setup() {
    // mounted
    onMounted(() => {
      console.log('Component is mounted!')
    })
  }
}

 ```

#### 其他一些改变

 1. slot改为v-slot
 2. Vue 3 有 createApp()，而 Vue 2 的是 new Vue()
 3. 改变了vue2中.sync的写法

 ```

 vue2中的写法
 <component :value.sync="x"/>

 vue3中的写法
 <component v-model:value="x"/>

 ```

#### 未完待续，随时补充

#### 更多的不同，可以参考[vue3官方迁移指南](https://v3.cn.vuejs.org/guide/migration/introduction.html#%E6%A6%82%E8%A7%88)
