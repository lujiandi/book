### Vue

#### 创建 Vue 实例

```
<div id="app">
  {{ message }}
</div>
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})

```

挂载点： el 属性对应的 dom 元素

模版：挂载点内部的所有内容，还可以把模版放在 Vue 实例中定义，作为 `template` 属性值

实例：定义挂载点，把定义的数据与模版结合起来生成要展示的内容，再把这个内容放在挂载点中。vue 只会对它所对应的挂载点内的内容产生作用

#### Vue 实例中的数据,事件和方法

1. 插值:

   #文本

   \{\{\}\}

   ```
    <span>Message:{{msg}}</span>
   ```

   #原始 HTML

   双大括号会将数据解释为普通文本，而非 HTML 代码。为了输出真正的 HTML，你需要使用 `v-html` 指令

   ```
    <p>Using v-html directive: <span v-html="rawHtml"></span></p>
   ```

2. 事件处理:

   可以用 `v-on:`(简写为:`@`) 指令监听 DOM 事件，并在触发时运行一些 JavaScript 代码。函数定义在`methods`属性里

```
 <!-- `greet` 是在下面定义的方法名 -->
<button v-on:click="greet">Greet</button>
```

3. 数据

   定义在`data`属性里,在方法里访问数据 `this.属性名`

#### Vue 中的属性绑定和双向数据绑定

1. 属性绑定:

   `v-bind:`(简写为:`:`)绑定元素特性

   ```
    <span v-bind:title="message">
   ```

2. 双向数据绑定:

   表单元素`v-model="数据的属性名"`

#### Vue 中的计算属性和侦听器

1. 计算属性(`computed`)：

   模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护。对于任何复杂逻辑，你都应当使用计算属性。

   ```

     <div id="example">
       <p>Original message: "{{ message }}"</p>
       <p>Computed reversed message: "{{ reversedMessage }}"</p>
     </div>
     #
     var vm = new Vue({
       el: '#example',
       data: {
         message: 'Hello'
       },
       computed: {
         // 计算属性的 getter
         reversedMessage: function () {
           // `this` 指向 vm 实例
           return this.message.split('').reverse().join('')
         }
       }
     })

   ```

#### v-if, v-show 与 v-for 指令

`v-for`

```
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: '学习 JavaScript' },
      { text: '学习 Vue' },
      { text: '整个牛项目' }
    ]
  }
})

```
