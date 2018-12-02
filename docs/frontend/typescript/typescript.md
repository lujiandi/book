### TypeScript-本地 compiler(ts 转成 js)

- npm install typescript -g
- tsc xxx.ts

### 字符串模板

```
  ` ${表达式} `
```

### 析构表达式

```
 {属性名,属性名}=对象
```

### 参数

- 参数类型

  ```
    // 参数:后指定类型，类型可以是自定义类型
    var name:string = 'kiss';
    var a:any ='sss';
    var age:number =10;
    var f:boolean =true;
    function test(name:string):string{
      return 'sdfsdf';
    }

  ```

- 默认值
  - 参数:类型=默认值
