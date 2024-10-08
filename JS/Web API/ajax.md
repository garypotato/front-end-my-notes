# ajax 知识点

## XMLHttpRequest

```javascript
const xhr = new XMLHttpRequest();
xhr.open('GET', '/api', false);
xhr.onreadystatechange = function () {
	// 这里的函数异步执行，可参考之前 JS 基础中的异步模块
	if (xhr.readyState === 4) {
		if (xhr.status === 200) {
			alert(xhr.responseText);
		}
	}
};
xhr.send(null);
```

有同学可能疑问：难道考我们这个，去上班就一定让我们写原生 JS 不让用 jquery 吗？———— 当然不是，这就是考察基础知识掌握的一种手段，而掌握好了基础知识，并不一定马上就用上，但是一定会增加你干活的效率。毕竟招你进来是干活的，要快！

## 状态码说明

### readyState

xhr.readyState 的状态吗说明

- 0 - (未初始化）还没有调用 send()方法
- 1 -（载入）已调用 send()方法，正在发送请求
- 2 -（载入完成）send()方法执行完成，已经接收到全部响应内容
- 3 -（交互）正在解析响应内容
- 4 -（完成）响应内容解析完成，可以在客户端调用了

### status

http 状态吗有 `2xx` `3xx` `4xx` `5xx` 这几种，比较常用的有以下几种

- 200 正常
- 301 永久重定向；302 临时重定向；304 资源未被修改；
- 404 找不到资源；403 权限不允许；
- 5xx 服务器端出错了

## 跨域

### 什么是跨域

浏览器中有“同源策略”，即一个域下的页面中，无法通过 ajax 获取到其他域的接口。例如慕课网有一个接口`http://m.imooc.com/course/ajaxcourserecom?cid=459`，你自己的一个页面`http://www.yourname.com/page1.html`中的 ajax 无法获取这个接口。这正是命中了“同源策略”。如果浏览器哪些地方忽略了同源策略，那就是浏览器的安全漏洞，需要紧急修复。

url 哪些地方不同算作跨域？

- 协议
- 域名
- 端口

但是 html 中几个标签能逃避过同源策略——`<script src="xxx">`、`<img src="xxxx"/>`、`<link href="xxxx">`，这俩标签的 `src` 或 `href` 可以加载其他域的资源，不受同源策略限制。

因此，这是三个标签可以做一些特殊的事情。

- `<img>`可以做打点统计，因为统计方并不一定是同域的，在讲解 JS 基础知识异步的时候有过代码示例。除了能跨域之外，`<img>`几乎没有浏览器兼容问题，它是一个非常古老的标签。
- `<script>`和`<link>`可以使用 CDN，CDN 基本都是其他域的链接。
- 另外`<script>`还可以实现 JSONP，能获取其他域接口的信息，接下来马上讲解。

但是请注意，所有的跨域请求方式，最终都需要信息提供方来做出相应的支持和改动，也就是要经过信息提供方的同意才行，否则接收方是无法得到他们的信息的，浏览器是不允许的。

### JSONP

首先，有一个概念要你要明白，例如访问`http://coding.m.imooc.com/classindex.html`的时候，服务器端就一定有一个`classindex.html`文件吗？—— 不一定，服务器可以拿到这个请求，然后动态生成一个文件，然后返回。
同理，`<script src="http://coding.m.imooc.com/api.js">`也不一定加载一个服务器端的静态文件，服务器也可以动态生成文件并返回。OK，接下来正式开始。

例如我们的网站和慕课网，肯定不是一个域。我们需要慕课网提供一个接口，供我们来获取。首先，我们在自己的页面这样定义

```html
<script>
	window.callback = function (data) {
		// 这是我们跨域得到信息
		console.log(data);
	};
</script>
```

然后慕课网给我提供了一个`http://coding.m.imooc.com/api.js`，内容如下（之前说过，服务器可动态生成内容）

```js
callback({ x: 100, y: 200 });
```

最后我们在页面中加入`<script src="http://coding.m.imooc.com/api.js"></script>`，那么这个 js 加载之后，就会执行内容，我们就得到内容了

### jQuery 实现 jsonp

提供方提供的数据：

```js
callback({
	x: 100,
	y: 200,
});
```

接收方的写法：

```js
$.ajax({
	url: 'http://localhost:8882/x-origin.json',
	dataType: 'jsonp',
	jsonpCallback: 'callback',
	success: function (data) {
		console.log(data);
	},
});
```

### 服务器端设置 http header

这是需要在服务器端设置的，作为前端工程师我们不用详细掌握，但是要知道有这么个解决方案。而且，现在推崇的跨域解决方案是这一种，比 JSONP 简单许多。

```js
response.setHeader('Access-Control-Allow-Origin', 'http://localhost:8011'); // 第二个参数填写允许跨域的域名称，不建议直接写 "*"
response.setHeader('Access-Control-Allow-Headers', 'X-Requested-With');
response.setHeader('Access-Control-Allow-Methods', 'PUT,POST,GET,DELETE,OPTIONS');

// 接收跨域的cookie
response.setHeader('Access-Control-Allow-Credentials', 'true');
```

<hr style="border: 10px solid green" />

# ajax 解答

## 手动编写一个 ajax，不依赖第三方库

```js
function ajax(url, successFn) {
	const xhr = new XMLHttpRequest();
	xhr.open('GET', url, false);
	xhr.onreadystatechange = function () {
		// 这里的函数异步执行，可参考之前 JS 基础中的异步模块
		if (xhr.readyState == 4) {
			if (xhr.status == 200) {
				successFn(xhr.responseText);
			}
		}
	};
	xhr.send(null);
}
```

## 跨域的几种实现方式

- JSONP
- 服务器端设置 http header
