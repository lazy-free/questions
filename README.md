# 前端问题整理

---

###### 1. 输出是什么？

```javascript
function fn() {
  console.log(a);
  console.log(b);
  var a = 'a';
  let b = 'b';
}

fn();
```

<details>
<summary><b>答案</b></summary>
<p>
#### 答案: `undefined` 和 `ReferenceError`

在函数内部，我们首先通过 `var` 关键字声明了 `name` 变量。这意味着变量被提升了（内存空间在创建阶段就被设置好了），直到程序运行到定义变量位置之前默认值都是 `undefined`。因为当我们打印 `name` 变量时还没有执行到定义变量的位置，因此变量的值保持为 `undefined`。

通过 `let` 和 `const` 关键字声明的变量也会提升，但是和 `var` 不同，它们不会被<i>初始化</i>。在我们声明（初始化）之前是不能访问它们的。这个行为被称之为暂时性死区。当我们试图在声明之前访问它们时，JavaScript 将会抛出一个 `ReferenceError` 错误。

</p>
</details>

---

###### 2. 输出是什么？

```javascript
['1', '2', '3'].map(parseInt);
```

<details>
<summary><b>答案</b></summary>
<p>
#### 答案: [1, NaN, NaN]

map 函数的第一个参数 callback,可以接收三个参数，其中第一个参数代表当前被处理的元素，而第二个参数代表该元素的索引。
parseInt 是用来解析字符串，使字符串成为指定基数的整数`parseInt(string, radix)`,接收两个参数，第一个表示被处理的值（字符串），第二个表示为解析时的基数。

</p>
</details>

---

###### 3. 事件循环，输出顺序

```javascript
async function async1() {
  console.log('async1 start');
  await async2();
  console.log('async1 end');
}
async function async2() {
  console.log('async2');
}
console.log('script start');
setTimeout(function () {
  console.log('setTimeout');
}, 0);
async1();
new Promise(function (resolve) {
  console.log('promise1');
  resolve();
}).then(function () {
  console.log('promise2');
});
console.log('script end');
```

<details>
<summary><b>答案</b></summary>
<p>

#### 答案: `script start` => `async1 start` => `async2` => `promise1` => `script end` => `async1 end` => `promise2` => `setTimeout`

## 任务队列

- JS 分为同步任务和异步任务
- 同步任务都在主线程上执行，形成一个执行栈
- 主线程之外，事件触发线程管理着一个任务队列，只要异步任务有了运行结果，就在任务队列之中放置一个事件。
- 一旦执行栈中的所有同步任务执行完毕（此时 JS 引擎空闲），系统就会读取任务队列，将可运行的异步任务添加到可执行栈中，开始执行。

根据规范，事件循环是通过任务队列的机制来进行协调的。一个 Event Loop 中，可以有一个或者多个任务队列(task queue)，一个任务队列便是一系列有序任务(task)的集合；每个任务都有一个任务源(task source)，源自同一个任务源的 task 必须放到同一个任务队列，从不同源来的则被添加到不同队列。 setTimeout/Promise 等 API 便是任务源，而进入任务队列的是他们指定的具体执行任务。

## 宏任务

- (macro)task（又称之为宏任务），可以理解是每次执行栈执行的代码就是一个宏任务（包括每次从事件队列中获取一个事件回调并放到执行栈中执行）。

- 浏览器为了能够使得 JS 内部(macro)task 与 DOM 任务能够有序的执行，会在一个(macro)task 执行结束后，在下一个(macro)task 执行开始前，对页面进行重新渲染，流程如下：

`(macro)task->渲染->(macro)task->...`

- (macro)task 主要包含：script(整体代码)、setTimeout、setInterval、I/O、UI 交互事件、postMessage、MessageChannel、setImmediate(Node.js 环境)

## 微任务

- microtask（又称为微任务），可以理解是在当前 task 执行结束后立即执行的任务。也就是说，在当前 task 任务后，下一个 task 之前，在渲染之前。

- 所以它的响应速度相比 setTimeout（setTimeout 是 task）会更快，因为无需等渲染。也就是说，在某一个 macrotask 执行完后，就会将在它执行期间产生的所有 microtask 都执行完毕（在渲染前）。

- microtask 主要包含：Promise.then、MutaionObserver、process.nextTick(Node.js 环境)

## 运行机制

在事件循环中，每进行一次循环操作称为 tick，每一次 tick 的任务处理模型是比较复杂的，但关键步骤如下：

- 执行一个宏任务（栈中没有就从事件队列中获取）
- 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
- 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
- 当前宏任务执行完毕，开始检查渲染，然后 GUI 线程接管渲染
- 渲染完毕后，JS 线程继续接管，开始下一个宏任务（从事件队列中获取）

![Image text](https://github.com/lazy-free/questions/blob/main/static/%E4%BA%8B%E4%BB%B6%E5%BE%AA%E7%8E%AF%E6%B5%81%E7%A8%8B.png)

## 微任务与宏任务

##### 宏任务主要包含：script、setTimeout、setInterval、I/O、UI 交互事件、setImmediate(Node.js 环境)

##### 微任务主要包含：promise.then() 、MutaionObserver、process.nextTick(Node.js 环境)

</p>
</details>

---

###### 4. 实现函数防抖（debounce）

函数防抖（debounce）：当持续触发事件时，一定时间段内没有再触发事件，事件处理函数才会执行一次，如果设定的时间到来之前，又一次触发了事件，就重新开始延时。

<details>
<summary><b>答案</b></summary>
<p>

#### 答案:

```javascript
function debounce(fn) {
  let timeout = null;
  return function () {
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      fn.apply(this, arguments);
    }, 500);
  };
}

var ipt = document.getElementById('ipt');
ipt.addEventListener(
  'input',
  debounce(() => console.log(1))
);
```

</p>
</details>

---
