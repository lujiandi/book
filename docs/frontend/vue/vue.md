# Vue

#### 创建 Vue 实例

> 把定义的数据与模版结合起来生成要展示的内容，再把这个内容放在挂载点中。vue 只会对它所对应的挂载点内的内容产生作用.

> 数据定义在`data`属性里,在方法里访问数据 `this.属性名`

> 方法定义在`methods`属性里。

```
<div id="app">
  {{ message }}
</div>
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  },
  methods:{

  },
  watch:{

  },
  computed:{

  }
})

```

#### \{\{\}\} `v-text` `v-html`

\{\{\}\}

> 显示文本

<code><span>Message:\{\{msg\}\}</span></code>

`v-text` `v-html`

> `v-html`将文本解析成 html 结构

```
<div v-text="hello"></div>
<div v-html="template"></div>
```

#### `v-bind`

> 元素节点的属性绑定，简写`:`

```
<a v-bind:href="url" target="_blank">imooc</a>
<a :href="url" target="_blank">imooc</a><br />
```

#### `v-model`

> 双向数据绑定,表单元素`v-model="数据的属性名"`

#### `v-on`

> 事件绑定，简写`@`

```
<p>
  count:{{ count }} <button v-on:click="plus()">+</button
  ><button @click="reduce()">-</button>
</p>

```

#### `v-if`

> 条件表达式

```
<!-- if-elseif-else -->
<div v-if="count>0">count的值大于0</div>
<div v-else-if="count==0">count的值等于0</div>
<div v-else>count的值小于0</div>
```

#### `v-show`

```
<p>
  <i v-show="show"
    >{{ hello }}<button @click="showAndHidden">隐藏</button></i
  >
  <i v-show="!show"><button @click="showAndHidden">显示</button></i>
</p>
```

#### `v-for`

> 循环

```
<ul>
  <li v-for="item in list">{{ item }}</li>
</ul>
```

#### watch

> 监听器，Vue 实例的 watch 属性对象，

```
 // 监听
  watch: {
    // 监听已有的属性
    count: function(newVal, oldVal) {
      console.log('newVal:' + newVal + '  oldVal:' + oldVal);
    }
  }
```

#### computed

> 计算属性，Vue 实例的 computed 属性对象

```
//计算
computed: {
  // 由已有属性(可以是多个)计算出新的值,已有属性改变后重新计算
  hello1: function() {
    return this.hello.toLocaleUpperCase();
  }
}
```

#### 元素节点的样式

`style`

> 绑定`style`,值为样式对象。

```
style: {
  color: 'red'
}
```

`class`

> 绑定`class`,值可以为数组。条件表达式要写成对象形式，语法:`{'className':条件表达式}`

```
<div :style="style" :class="['actived',{'sddf':count>0} ]">
  {{ hello }}
</div>
```

#### Vue Router

> 路由管理

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <title>Document</title>
    <script src="https://unpkg.com/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
  </head>
  <body>
    <div id="app">
      <h1>Hello App!</h1>
      <p>
        <!-- 使用 router-link 组件来导航. -->
        <!-- 通过传入 `to` 属性指定链接. -->
        <!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
        <router-link to="/foo">Go to Foo</router-link>
        <router-link to="/bar">Go to Bar</router-link>
      </p>
      <!-- 路由出口 -->
      <!-- 路由匹配到的组件将渲染在这里 -->
      <router-view></router-view>
    </div>
    <script>
      // 0. 如果使用模块化机制编程，导入Vue和VueRouter，要调用 Vue.use(VueRouter)

      // 1. 定义 (路由) 组件。
      // 可以从其他文件 import 进来
      const Foo = { template: '<div>foo</div>' };
      const Bar = { template: '<div>bar</div>' };

      // 2. 定义路由
      // 每个路由应该映射一个组件。 其中"component" 可以是
      // 通过 Vue.extend() 创建的组件构造器，
      // 或者，只是一个组件配置对象。
      // 我们晚点再讨论嵌套路由。
      const routes = [
        { name: 'foo', path: '/foo', component: Foo },
        { name: 'bar', path: '/bar', component: Bar }
      ];

      // 3. 创建 router 实例，然后传 `routes` 配置
      // 你还可以传别的配置参数, 不过先这么简单着吧。
      const router = new VueRouter({
        routes // (缩写) 相当于 routes: routes
      });

      // 4. 创建和挂载根实例。
      // 记得要通过 router 配置参数注入路由，
      // 从而让整个应用都有路由功能
      const app = new Vue({
        router
      }).$mount('#app');

      // 现在，应用已经启动了！
    </script>
  </body>
</html>


```

#### Vue-CLI

安装：npm install -g @vue/cli

生成项目：vue create projectName
