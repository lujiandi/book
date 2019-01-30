## React

### React 绑定 this 的三种方式

1. 构造函数内绑定:

   在构造函数 constructor 内绑定 this，好处是仅需要绑定一次，避免每次渲染时都要重新绑定，函数在别处复用时也无需再次绑定

   ```
   import React, {Component} from 'react'

   class Test extends React.Component {
       constructor (props) {
           super(props)
           this.state = {message: 'Allo!'}
           this.handleClick = this.handleClick.bind(this)
       }

       handleClick (e) {
           console.log(this.state.message)
       }

       render () {
           return (
               <div>
                   <button onClick={ this.handleClick }>Say Hello</button>
               </div>
           )
       }
   }

   ```

2. 箭头函数:

   箭头函数则会捕获其所在上下文的 this 值，作为自己的 this 值，使用箭头函数就不用担心函数内的 this 不是指向组件内部了。

   ```
    class Test extends React.Component {
    constructor (props) {
        super(props)
        this.state = {message: 'Allo!'}
    }

    handleClick (e) {
        console.log(this.state.message)
    }

    render () {
        return (
            <div>
                <button onClick={ ()=>{ this.handleClick() } }>Say Hello</button>
            </div>
        )
    }
   }
   ```

   这种方式有个小问题，因为箭头函数总是匿名的，如果你打算移除监听事件，可以改用以下方式：

   ```
    class Test extends React.Component {
    constructor (props) {
        super(props)
        this.state = {message: 'Allo!'}
    }

    handleClick = (e) => {
        console.log(this.state.message)
    }

    render () {
        return (
            <div>
                <button onClick={ this.handleClick }>Say Hello</button>
            </div>
        )
    }
   }

   ```
