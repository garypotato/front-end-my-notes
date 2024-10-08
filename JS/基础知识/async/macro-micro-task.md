# 宏任务和微任务

## 介绍

- 宏任务：setTimeout setInterval DOM 事件
- 微任务：Promise（对于前端来说）
- 微任务比宏任务执行的更早

```js
console.log(100);
setTimeout(() => {
	console.log(200);
});
Promise.resolve().then(() => {
	console.log(300);
});
console.log(400);
// 100 400 300 200
```

## event loop 和 DOM 渲染

再次回顾 event loop 的过程

- 每一次 call stack 结束，都会触发 DOM 渲染（不一定非得渲染，就是给一次 DOM 渲染的机会！！！）
- 然后再进行 event loop

```js
const $p1 = $("<p>一段文字</p>");
const $p2 = $("<p>一段文字</p>");
const $p3 = $("<p>一段文字</p>");
$("#container").append($p1).append($p2).append($p3);

console.log("length", $("#container").children().length);
alert("本次 call stack 结束，DOM 结构已更新，但尚未触发渲染");
// （alert 会阻断 js 执行，也会阻断 DO M 渲染，便于查看效果）
// 到此，即本次 call stack 结束后（同步任务都执行完了），浏览器会自动触发渲染，不用代码干预

// 另外，按照 event loop 触发 DOM 渲染时机，setTimeout 时 alert ，就能看到 DOM 渲染后的结果了
setTimeout(function () {
	alert("setTimeout 是在下一次 Call Stack ，就能看到 DOM 渲染出来的结果了");
});
```

## 宏任务和微任务的区别

- 宏任务：DOM 渲染后再触发
- 微任务：DOM 渲染前会触发

```js
// 修改 DOM
const $p1 = $("<p>一段文字</p>");
const $p2 = $("<p>一段文字</p>");
const $p3 = $("<p>一段文字</p>");
$("#container").append($p1).append($p2).append($p3);

// // 微任务：渲染之前执行（DOM 结构已更新）
// Promise.resolve().then(() => {
//     const length = $('#container').children().length
//     alert(`micro task ${length}`)
// })

// 宏任务：渲染之后执行（DOM 结构已更新）
setTimeout(() => {
	const length = $("#container").children().length;
	alert(`macro task ${length}`);
});
```

再深入思考一下：为何两者会有以上区别，一个在渲染前，一个在渲染后？

- 微任务：ES 语法标准之内，JS 引擎来统一处理。即，不用浏览器有任何关于，即可一次性处理完，更快更及时。
- 宏任务：ES 语法没有，JS 引擎不处理，浏览器（或 nodejs）干预处理。

![event](../../../assets/event%20loop%20+%20dom%20+%20micro%20task.png)
![whole event](../../../assets/event%20loop%20+%20dom%20+%20micro%20task%20+%20marco%20task.png)
