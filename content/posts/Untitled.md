---
title: "深入 Web 开发：异步编程"
date: "2020-10-22"
---

JavaScript 引擎是基于单线程（Single-threaded）事件循环的概念构建的，所有任务都被放在一个任务队列（job queue）中。同一时刻只允许执行一个任务，每当一个任务执行完毕后，事件循环（event loop）才会继续执行队列中的下一个任务，以此类推。

例如，一个任务队列里有 A 和 B 两个任务，A 排在 B 前面。A 需要请求一个响应时间为 3 秒的 API，而 B 只是做了一个简单的加减运算。当事件循环执行到 A 时，等待 A 响应 3 秒后才会执行 B，这种模式叫做同步编程。而当事件循环执行到 A 时，马上执行 B，直到 A 响应完成了，再回过头来处理 A ，这种模式叫做异步编程。

同步编程虽然易于理解，却会带来很多问题，比如一个网站的某个代码块执行时间过长，就会阻塞后面的代码块，造成页面卡顿，影响用户体验。因此，在实际的开发中，异步编程无处不在，最常用的是用来向服务器请求 API。

异步编程主要有事件模型、 回调函数、Async/Await、Observable 等方法，其中 Async/Await 被认为是 JavaScript 异步编程的最终解决方案。

<!--more-->

### 事件模型

事件模型是 JavaScript 最基础的异步编程形式，任务执行的先后顺序取决于某个事件是否发生。

```javascript
// 代码块 A
const button = document.getElementById("button");
button.addEventListener("click", () => console.log("Hello"));

// 代码块 B
console.log("World");
```

运行上面的代码后发现，明明 A 在 B 前面，却直接执行了 B 打印出 `World` ，直到点击了按钮才执行 A 才打印出 `Hello` 。

事件模型适用于处理简单的交互，对于复杂的业务场景来说却不是很灵活，很少人会用这种方法进行异步编程。

### 回调函数（Callback）

在 ECMAScript 2015 之前，开发者主要使用回调函数进行异步编程，比如使用 Ajax 请求 API。这种方式最大的问题是，嵌套过多很容易陷入回调地狱（Callback Hell），代码维护起来简直是噩梦。

```javascript
$.ajax({
  url: "https://api.github.com/users/giuhub",
  dataType: "json",
  type: "GET",
  success(resp) {
    console.log(resp);
    $.ajax({
      url: "https://api.github.com/users/giuhub/followers",
      dataType: "json",
      type: "GET",
      success(resp) {
        console.log(resp);
      },
      error(error) {
        console.log(error);
      },
    });
  },
  error(error) {
    console.log(error);
  },
});
```

如果想要实现更复杂的功能，回调函数的局限性就会显性出来。例如，并行执行两个异步操作，只取优先完成的操作结果。在这种情况下回调函数实现起来很困难，而 Promise 只需要使用 `Promise.race()` 方法即可。

### Promise

```javascript
const getUserData = new Promise((resolve, reject) => {
  $.ajax({
    url: "https://api.github.com/users/giuhub",
    dataType: "json",
    type: "GET",
    success(resp) {
      resolve(resp);
    },
    error(error) {
      reject(error);
    },
  });
});

const getUserFollowers = new Promise((resolve, reject) => {
  $.ajax({
    url: "https://api.github.com/users/giuhub/followers",
    dataType: "json",
    type: "GET",
    success(resp) {
      resolve(resp);
    },
    error(error) {
      reject(error);
    },
  });
});

getUserData
  .then((v) => {
    console.log(v);
    return getUserFollowers;
  })
  .then((v) => {
    console.log(v);
  });
```

每个 Promise 都会经历一个短暂的生命周期：先是处于待定（pending）的状态，一旦异步操作结束就会进入兑现（fulfilled）或拒绝（rejected）的状态。需要注意的是，一旦进入其中一种状态后就不可逆了。

{{< mermaid >}}
graph LR
A["Promise"]
B["pending"]
C["fulfilled"]
D["rejected"]
A-->B
B-->C
B-->D

{{< /mermaid >}}

#### 创建 Promise

用 Promise 构造函数可以创建一个新的 Promise，构造器函数接受一个执行器函数作为参数。该执行器函数也接受两个参数，分别是执行成功时调用的 `resolve()` 函数和执行失败时调用的 `reject()` 函数。

接下来，我们将使用 Promise 封装 ajax 请求。

```javascript
const getUserData = new Promise((resolve, reject) => {
  $.ajax({
    url: "https://api.github.com/users/giuhub",
    dataType: "json",
    type: "GET",
    success(resp) {
      resolve(resp);
    },
    error(error) {
      reject(error);
    },
  });
});
```

很简单的代码，如何使用呢？

#### 链式调用

##### Promise.prototype.then()

`then()` 方法接受两个可选的参数：第一个是 Promise 状态变为 fulfilled 时的回调函数；第二个是 Promise 状态变为 rejected 时的回调函数。

`then()` 方法返回一个新的 Promise 实例。上一个 Promise 状态发生变化后（fulfilled 或者 rejected），返回值会作为参数传递给下一个 `then()` 。

```javascript
let p1 = new Promise((resolve) => setTimeout(() => resolve(44), 6000));
let p2 = new Promise((resolve) => setTimeout(() => resolve(45), 3000));

p1.then((value) => {
  console.log(value);
  return p2;
}).then((value) => {
  console.log(value);
});

// 44
// 45
```

##### Promise.prototype.catch()

`catch()` 方法返回一个 Promise，该方法接受一个参数：Promise 状态变为 rejected 时的回调函数。事实上，`catch()` 方法和 `then(undefined, onRejected)` 的作用是一样的。

##### Promise.prototype.finally()

`finally()` 方法返回一个 Promise ，该方法接受一个参数：Promise 状态变为 fulfilled 或 rejected 时的回调函数。

```javascript
let p = Promise.resolve(42);

p.then((value) => console.log(value))
  .catch((error) => console.log(error))
  .finally(() => console.log(`返回状态为 resolved 或 rejected`));

// 42
// 返回状态为 resolved 或 rejected
```

#### 响应多个 Promise

##### Promise.all()

`Promise.all()` 接受一个参数并返回一个 Promise 实例，该参数是一个包含多个受监视 Promise 的可迭代对象（例如一个数组），只有当可迭代对象中所有 Promise 都被解决后返回的 Promise 实例才会被完成。

```javascript
let p1 = Promise.resolve(42);
let p2 = Promise.resolve(43);
let p3 = Promise.resolve(44);

let p4 = Promise.all([p1, p2, p3]);

p4.then((value) => console.log(value));

// [42, 43, 44]
```

这段代码中，p4 完成处理程序后的结果，是一个包含每个解决值的数组，这些值按照与可迭代对象中的 Promise 顺序一致。

可迭代对象中的 Promise 只要有一个被拒绝，那么返回的 Promise 实例就会立即被拒绝。

```javascript
let p1 = Promise.resolve(42);
let p2 = Promise.reject(43);
let p3 = Promise.resolve(44);

let p4 = Promise.all([p1, p2, p3]);

p4.then((value) => console.log(value));

// Uncaught (in promise) 43
```

##### Promise.race()

`Promise.race()` 和 `Promise.all()` 类似，也是接受一个参数并返回一个 Promise 实例，参数是一个以若干个 Promise 组成的可迭代对象。不同的地方是，`Promise.race()` 只要可迭代对象中有一个 Promise 被解决，返回的 Promise 实例就会被完成。

```javascript
let p1 = Promise.resolve(42);
let p2 = Promise.reject(43);
let p3 = Promise.resolve(44);

let p4 = Promise.race([p1, p2, p3]);

p4.then((value) => console.log(value));

// 42
```

### Generator

### Async

```javascript
const getUserData = new Promise((resolve, reject) => {
  $.ajax({
    url: "https://api.github.com/users/giuhub",
    dataType: "json",
    type: "GET",
    success(resp) {
      resolve(resp);
    },
    error(error) {
      reject(error);
    },
  });
});

const getUserFollowers = new Promise((resolve, reject) => {
  $.ajax({
    url: "https://api.github.com/users/giuhub/followers",
    dataType: "json",
    type: "GET",
    success(resp) {
      resolve(resp);
    },
    error(error) {
      reject(error);
    },
  });
});

const f = async () => {
  await getUserData.then((v) => console.log(v));
  await getUserFollowers.then((v) => console.log(v));
};
f();
```

async 函数是 JavaScript 异步编程的最终解决方案。

async 函数返回一个 Promise 对象。

async 函数内部 return 语句返回的值，会成为 `then()` 方法回调函数的参数。

```javascript
async function f() {
  return 42;
}

f()
  .then((v) => console.log(v))
  .catch((e) => console.log(e));

// 42
```

async 函数内部抛出错误，会导致返回的 Promise 对象变为 reject 状态。抛出的错误对象会被 `catch()` 方法回调函数或者是 `then()` 方法的第二个参数接收到。以 `catch()` 为例：

```javascript
async function f() {
  throw new Error("这是一个错误！");
}

f()
  .then((v) => console.log(v))
  .catch((e) => console.log(e));

// Error: 这是一个错误！
```

### Observable
