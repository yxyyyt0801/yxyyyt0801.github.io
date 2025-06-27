# 搭建项目

## vue-cli

- 全局安装 vue 命令行工具

  ```shell
  sudo npm install -g @vue/cli
  
  vue -V
  ```
  
- 创建本地项目

  ```shell
  # 进入创建目标项目的父级目录
  vue create goldmine-admin
  
  # 预设选择
  	- Please pick a preset: Manually select features
  	- Check the features needed for your project: Babel, PWA, Router, Vuex, CSS Pre-processors, Linter, Unit, E2E
  	- Choose a version of Vue.js that you want to start the project with 2.x
  	- Use history mode for router? (Requires proper server setup for index fallback in production) Yes
  	- Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Sass/SCSS (with dart-sass)
  	- Pick a linter / formatter config: Prettier
  	- Pick additional lint features: Lint on save
  	- Pick a unit testing solution: Jest
  	- Pick an E2E testing solution: Cypress
  	- Where do you prefer placing config for Babel, ESLint, etc.? In dedicated config files
  	- Save this as a preset for future projects? Yes
  	- Save preset as: test
  # 预设选择之后再创建项目时，直接加载test预设配置
  # vue create --preset test goldmine-admin
  
  # 选择系统预设 Default ([Vue 2] babel, eslint)
  overwrite
  ```
  
  
  
- 运行项目

  ```shell
  cd goldmine-admin
  npm run serve
  ```
  
- 修改 git 分支

  ```shell
  git branch -m master main
  ```
  
- GitHub 创建项目并同步

  - 创建repository：goldmine-admin
  
  - 同步本地项目
  
    ```shell
    # 同步前，拷贝GitHub 创建项目的配置文件：LICENSE、README.md
    cd goldmine-admin
    git remote add origin git@github.com:yxyyyt0801/goldmine-admin.git
    git push -u origin main -f
    
    git checkout -b develop main
    git push -u origin develop
    ```
    
    
  

# 导入和导出

## import

- 在一个文件或模块中可以有多个

- 全部导入

  ```javascript
  import * as _A from "./A.js" 
  ```

- import在引入文件路径时，引入一个依赖包，不需要相对路径。

  ```javascript
  import app from ‘app’;
  ```

  但引入一个自己写的 js 文件，需要相对路径。

  ```javascript
  import app from ‘./app.js’;
  ```

  引入第三方插件，不需要相对路径。

  ```javascript
  import Vue from 'vue';
  ```

- 导入css文件。

  ```javascript
  import 'vue-video-player/src/custom-theme.css';
  ```

  如果是在.vue文件中，需要在其外面套一个style

  ```vue
  <style>
    @import './test.css';
  </style>
  ```

- import '@…'的语句，@等价于 /src 这个目录，避免写麻烦而又易错的相对路径

  ```javascript
  import { getCodeImg } from "@/api/login";
  ```

- Vue使用import ... from ...来导入组件，库，变量等。而from后的来源可以是js，vue，json。在 webpack.base.conf.js 中设置；

  这3类可导入文件，js和vue是可以省略后缀，json不可以省略后缀；

  如果js和vue同名且在同一个文件夹下，js 优先级高于vue；

  ```javascript
  module.exports = {
        resolve: {
          extensions: ['.js', '.vue', '.json'],
          alias: {
            '@': resolve('src')
          }
        }
      ...
      }
  ```

  from后的来源除了文件，还可以是文件夹，那么在文件夹中的package.json存在且package.main设置正确的情况下，会默认加载package.json的package.main指定的js；若不满足，则加载index.js；若不满足，则加载index.vue。

  ```javascript
  import test from './components'
  ```

- 导入外部的模块，并立即执行

  ```javascript
  import './test'
  ```

- import 函数

  ```javascript
  // import()是运行时执行，即运行到这一句是便会加载指定的模块
  if (login = true){
       import ('./loginFun.js').then(...)
   }
  ```
  
  

## export

- 在一个文件或模块中可以有多个



## export default

- 在一个文件或模块中仅有一个
- 通过export方式导出，在导入import时要加花括号{ }，export default 则不需要 { }



# 生命周期

<img src="vue_overview.assets/vue_lifecycle.png" alt="vue_lifecycle" style="zoom:50%;" />



# 组件

在 vue 中，组件是可以重复使用的 **vue 实例**，所以它与 new Vue 接收相同的选项，例如 data、computed、watch、methods 以及生命周期钩子等。

- data 必须是一个函数，因此每个实例可以维护一份被返回对象的独立的拷贝
- computed 具有缓存功能；计算属性是基于它们的响应式依赖进行缓存的。只在相关响应式依赖（依赖的属性）发生改变时它们才会重新求值

```javascript
// 定义一个名为 button-counter 的新组件
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
})

<div id="components-demo">
  <button-counter></button-counter>
</div>
```



全局注册，也就是说它们在注册之后可以用在任何新创建的 Vue 根实例 (new Vue) 的模板中。

**注意**全局注册的行为必须在根 Vue 实例 (通过 new Vue) 创建之前发生。

```javascript
Vue.component('component-a', { /* ... */ })
Vue.component('component-b', { /* ... */ })
Vue.component('component-c', { /* ... */ })

new Vue({ el: '#app' })

<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```



局部注册。全局注册往往是不够理想的。比如，如果你使用一个像 webpack 这样的构建系统，全局注册所有的组件意味着即便你已经不再使用一个组件了，它仍然会被包含在你最终的构建结果中。这造成了用户下载的 JavaScript 的无谓的增加。

**注意**局部注册的组件在其子组件中不可用。

```javascript
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
var ComponentC = { /* ... */ }

new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```



而父子组件，是一种层级关系，其中一个组件（父组件）内部使用了另一个组件（子组件）。

- 父组件向子组件传递属性，子组件声明 props

- 每次父级组件发生变更时，子组件中所有的 prop 都将会刷新为最新的值。这意味着你不应该在一个子组件内部改变 prop。如果你这样做了，Vue 会在浏览器的控制台中发出警告。

  ```javascript
  Vue.component('blog-post', {
    props: ['post'],
    template: `
      <div class="blog-post">
        <h3>{{ post.title }}</h3>
        <div v-html="post.content"></div>
      </div>
    `
  })
  
  <blog-post
    v-for="post in posts"
    v-bind:key="post.id"
    v-bind:post="post"
  ></blog-post>
  ```

  

- 子组件通过$emit 方法触发自定义事件，父组件通过@childEvent （$on）监听子组件触发的自定义事件，并在事件处理函数中接收子组件传递的数据

  ```javascript
  <blog-post
    ...
    v-on:enlarge-text="postFontSize += 0.1"
  ></blog-post>
  
  <button v-on:click="$emit('enlarge-text')">
    Enlarge text
  </button>
  ```

  



# 插件

- 如果插件是一个对象，必须提供 install 方法。

- 如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。

- 该方法需要在调用 new Vue() 之前被调用。

  ```javascript
  // 调用 `MyPlugin.install(Vue)`
  Vue.use(MyPlugin)
  
  new Vue({
    // ...组件选项
  })
  ```

  

# 模板指令

指令 (Directives) 是带有 `v-` 前缀的特殊 attribute。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于 DOM。

一些指令能够接收一个“参数”，在指令名称之后以冒号表示。例如，`v-bind` 指令可以用于响应式地更新 HTML attribute。

- v-bind

  ```html
  <!-- Mustache {{ }} 语法不能作用在 HTML attribute 上，遇到这种情况应该使用 v-bind 指令
  	v-bind:html属性="vue表达式"
  	在这里 id 是参数，告知 v-bind 指令将该元素的 id attribute 与表达式 dynamicId 的值绑定 -->
  <div v-bind:id="dynamicId"></div>
  
  <!-- 方括号 [] 内的是动态参数 -->
  <a v-bind:[attributeName]="url"> ... </a>
  
  <!-- 缩写 -->
  <a :href="url">...</a>
  ```




- v-on

  ```html
  <!-- 完整语法 -->
  <a v-on:click="doSomething">...</a>
  
  <!-- 缩写 -->
  <a @click="doSomething">...</a>
  
  <!-- 动态参数的缩写 (2.6.0+) -->
  <a @[event]="doSomething"> ... </a>
  
  <!-- 修饰符 (modifier) 是以半角句号 . 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定 -->
  <form v-on:submit.prevent="onSubmit">...</form>
  ```



- v-model

  - v-model 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。
  - v-model 会忽略所有表单元素的 value、checked、selected attribute 的初始值而总是将 Vue 实例的数据作为数据来源。你应该通过 JavaScript 在组件的 data 选项中声明初始值。
  - v-model 在内部为不同的输入元素使用不同的 property 并抛出不同的事件：
    - text 和 textarea 元素使用 `value` property 和 `input` 事件；
    - checkbox 和 radio 使用 `checked` property 和 `change` 事件；
    - select 字段将 `value` 作为 prop 并将 `change` 作为事件。

  ```html
  <input v-model="message" placeholder="edit me">
  <p>Message is: {{ message }}</p>
  ```

  
