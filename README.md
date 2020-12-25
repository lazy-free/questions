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

## 微任务与宏任务
# 宏任务主要包含：script、setTimeout、setInterval、I/O、UI交互事件、setImmediate(Node.js 环境)
# 微任务主要包含：Promise、MutaionObserver、process.nextTick(Node.js 环境)

![Image text](https://github.com/lazy-free/questions/blob/main/static/%E4%BA%8B%E4%BB%B6%E5%BE%AA%E7%8E%AF%E6%B5%81%E7%A8%8B.png)

</p>
</details>

---
