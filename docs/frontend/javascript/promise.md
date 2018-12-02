## Promise

![promise](images/Promise.jpg)

## async function

- async function 声明将定义一个返回 AsyncFunction 对象的异步函数。
- 当调用一个 async 函数时，会返回一个 Promise 对象。当这个 async 函数返回一个值时，Promise 的 resolve 方法会负责传递这个值；当 async 函数抛出异常时，Promise 的 reject 方法也会传递这个异常值。
- async 函数中可能会有 await 表达式，这会使 async 函数暂停执行，等待表达式中的 Promise 解析完成后继续执行 async 函数。
- 注意 await 关键字仅仅在 async function 中有效。如果在 async function 函数体外使用 await ，你只会得到一个语法错误（SyntaxError）。
- async/await 的用途是简化使用 promises 异步调用的操作，并对一组 Promises 执行某些操作。正如 Promises 类似于结构化回调，async/await 类似于组合生成器和 promises。

### await

- 如果一个 Promise 被传递给一个 await 操作符，await 将等待 Promise 正常处理完成,其回调的 resolve 函数参数作为 await 表达式的值

```
 function resolveAfter2Seconds(x) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x);
    }, 2000);
  });
}

async function f1() {
  var x = await resolveAfter2Seconds(10);
  console.log(x); // 10
}
f1();
```

- 如果该值不是一个 Promise，await 会把该值转换为已正常处理的 Promise，然后等待其处理结果。

```
async function f2() {
  var y = await 20;
  console.log(y); // 20
}
f2();
```

- 如果 Promise 处理异常，则异常值被抛出。

```
async function f3() {
  try {
    var z = await Promise.reject(30);
  } catch (e) {
    console.log(e); // 30
  }
}
f3();
```

### async function 示例

```
function resolveAfter2Seconds(x) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x);
    }, 2000);
  });
}

async function add1(x) {
  var a = await resolveAfter2Seconds(20);
  var b = await resolveAfter2Seconds(30);
  return x + a + b;
}

add1(10).then(v => {
  console.log(v); // prints 60 after 4 seconds.
});

async function add2(x) {
  var a = resolveAfter2Seconds(20);
  var b = resolveAfter2Seconds(30);
  return x + await a + await b;
}

add2(10).then(v => {
  console.log(v);  // prints 60 after 2 seconds.
});
```
