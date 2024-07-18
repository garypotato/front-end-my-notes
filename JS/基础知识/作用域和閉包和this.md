# 作用域和闭包 - 知识点

## 作用域

所谓作用域，即一个变量的合法使用范围

```js
let a = 0;
function fn1() {
	let a1 = 100;

	function fn2() {
		let a2 = 200;

		function fn3() {
			let a3 = 300;
			return a + a1 + a2 + a3;
		}
		fn3();
	}
	fn2();
}
fn1();
```

作用域分类

- 全局作用域：在全局定义的变量，全局都可用，像 document
- 函数作用域：在某个函数中定义的变量，只能用于当前函数，像 a b
- 块级作用域（ES6）：只能活跃于当前的块，示例如下

```js
// ES6 块级作用域
if (true) {
	let x = 100;
}
console.log(x); // 会报错
```

---

## 自由变量

- 一个变量在该作用域没有被定义，但被使用
- 向上级作用域去寻找该变量，层层往上找 —— 如上面的示例

---

## 闭包

闭包 —— 作用域应用的一个特殊情况。一般有两种书写形式：

- 函数作为参数被传入
- 函数作为返回值

```js
// 函数作为返回值
function create() {
	let a = 100;
	return function () {
		console.log(a);
	};
}
let fn = create();
let a = 200;
fn(); // 100

// 函数作为参数
function print(fn) {
	let a = 200;
	fn();
}
let a = 100;
function fn() {
	console.log(a);
}
print(fn); // 100
```

以上代码得出

- 自由变量的查找，是在函数定义的地方，向上级作用域查找
- 要在 **函数定义的地方（不是执行的地方)** 去寻找上级作用域！！！

## 闭包示例

```js
// 隐藏数据，只提供 API
function createCache() {
	let data = {}; // 闭包中的数据，被隐藏，不被外界访问
	return {
		set: function (key, val) {
			data[key] = val;
		},
		get: function (key) {
			return data[key];
		},
	};
}
let c = createCache();
c.set('a', 100);
console.log(c.get('a'));
```

---

## this

- 作为普通函数调用
- 使用 `call` `apply` `bind`
- 作为对象方法调用
- 在 class 的方法中调用
- 箭头函数

```js
// this 场景题 - 1
function fn1() {
	console.log(this);
}
fn1(); // 打印什么

fn1.call({ x: 100 }); // 打印什么

const fn2 = fn1.bind({ x: 200 });
fn2(); // 打印什么

// this 场景题 - 2
const zhangsan = {
	name: '张三',
	sayHi() {
		console.log(this); // 当前对象
	},
	wait() {
		setTimeout(function () {
			console.log(this); // window
		});
	},
	waitAgain() {
		setTimeout(() => {
			console.log(this); // 当前对象
		});
	},
};
zhangsan.sayHi(); // 打印什么
zhangsan.wait(); // 打印什么
zhangsan.waitAgain(); // 打印什么

// this 场景题 - 2
class People {
	constructor(name) {
		this.name = name;
		this.age = 20;
	}
	sayHi() {
		console.log(this);
	}
}
const zhangsan = new People('张三');
zhangsan.sayHi(); // 打印什么
```

<hr style="border: 10px solid green" />

# 作用域和闭包 - 解答

## this 的不同应用场景

- 作为普通函数调用
- 使用 `call` `apply` `bind`
- 作为对象方法调用
- 在 class 的方法中调用
- 箭头函数

## 创建 10 个`<a>`标签，点击的时候弹出来对应的序号

```js
// 创建 10 个`<a>`标签，点击的时候弹出来对应的序号
let i, a;
for (i = 0; i < 10; i++) {
	a = document.createElement('a');
	a.innerHTML = i + '<br>';
	a.addEventListener('click', function (e) {
		e.preventDefault();
		alert(i);
	});
	document.body.appendChild(a);
}
```

怎么解决？—— 把 `let` 移动到 `for` 里即可, 形成新的块作用域

```js
let a;
for (let i = 0; i < 10; i++) {
	a = document.createElement('a');
	a.innerHTML = i + '<br>';
	a.addEventListener('click', function (e) {
		e.preventDefault();
		alert(i);
	});
	document.body.appendChild(a);
}
```

## 实际开发中闭包的应用

参考之前的示例。再次强调闭包中自由变量的寻找方式！！！

```js
// 隐藏数据，只提供 API
function createCache() {
	let data = {}; // 闭包中的数据，被隐藏，不被外界访问
	return {
		set: function (key, val) {
			data[key] = val;
		},
		get: function (key) {
			return data[key];
		},
	};
}
let c = createCache();
c.set('a', 100);
console.log(c.get('a'));
```

## 手写 bind 函数

先回顾 bind 函数的应用

```js
function fn1(a, b) {
	console.log('this', this);
	console.log(a, b);
}
const fn2 = fn1.bind({ x: 100 }, 10, 20); // bind 返回一个函数，并会绑定上 this
fn2();
```

手写 bind

```js
Function.prototype.bind1 = function () {
	// 将参数解析为数组
	const args = Array.prototype.slice.call(arguments);

	// 获取 this（取出数组第一项，数组剩余的就是传递的参数）
	const t = args.shift();

	const self = this; // 当前函数

	// 返回一个函数
	return function () {
		// 执行原函数，并返回结果
		return self.apply(t, args);
	};
};
```

上面的例子只綁定了 build time 時的 args，不包括 call time 時的 args
下面是完美版本

```js
Function.prototype.myBind = function (context, ...args) {
	// 判断调用myBind的是否为函数
	if (typeof this !== 'function') {
		throw new TypeError('Function.prototype.myBind - 被调用的对象必须是函数');
	}

	// 如果没有传入上下文对象，则默认为全局对象
	// ES11 引入了 globalThis，它是一个统一的全局对象
	// 无论在浏览器还是 Node.js 中，都可以使用 globalThis 来访问全局对象。
	context = context || globalThis;

	// 保存原始函数的引用，this就是要绑定的函数
	const _this = this;

	// 返回一个新的函数作为绑定函数
	return function fn(...innerArgs) {
		// 判断返回出去的函数有没有被new
		if (this instanceof fn) {
			return new _this(...args, ...innerArgs);
		}
		// 使用apply方法将原函数绑定到指定的上下文对象上
		return _this.apply(context, args.concat(innerArgs));
	};
};
```

調用例子

```js
function example(a, b) {
	return this.value + a + b;
}

const obj = { value: 10 };
const boundExample = example.bind1(obj, 5);

console.log(boundExample(15)); // Outputs: 30 (10 + 5 + 15)
```

## 手写 call

调用

```js
// 定义一个对象
const person1 = {
	name: 'Alice',
	greet: function () {
		console.log(`Hello, ${this.name}!`);
	},
};

// 定义另一个对象
const person2 = {
	name: 'Bob',
};

// 使用call方法将person1的greet方法应用到person2上
person1.greet.call(person2); // 输出：Hello, Bob!
```

手写 call

```js
Function.prototype.myCall = function (context, ...args) {
	// 判断调用myCall的是否为函数
	if (typeof this !== 'function') {
		throw new TypeError('Function.prototype.myCall - 被调用的对象必须是函数');
	}

	// 如果没有传入上下文对象，则默认为全局对象
	// ES11 引入了 globalThis，它是一个统一的全局对象
	// 无论在浏览器还是 Node.js 中，都可以使用 globalThis 来访问全局对象。
	context = context || globalThis;

	// 用Symbol来创建唯一的fn，防止名字冲突
	let fn = Symbol('key');

	// this是调用myCall的函数，将函数绑定到上下文对象的新属性上
	context[fn] = this;

	// 传入MyCall的多个参数
	const result = context[fn](...args);

	// 将增加的fn方法删除
	delete context[fn];

	return result;
};
```
