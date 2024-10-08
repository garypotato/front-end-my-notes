# 知识点

## 浏览器加载资源的过程

### 加载资源的形式

- 输入 url 加载 html
- http://coding.m.imooc.com
- 加载 html 中的静态资源
- `<script src="/static/js/jquery.js"></script>`

### 加载一个资源的过程

- 浏览器根据 DNS 服务器得到域名的 IP 地址
- 向这个 IP 的机器发送 http 请求
- 服务器收到、处理并返回 http 请求
- 浏览器得到返回内容

## 浏览器渲染页面的过程

- 根据 HTML 结构生成 DOM Tree
- 根据 CSS 生成 CSS Rule
- 将 DOM 和 CSSOM 整合形成 RenderTree
- 根据 RenderTree 开始渲染和展示
- 遇到`<script>`时，会执行并阻塞渲染

## 几个示例

### 最简单的页面

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Document</title>
	</head>
	<body>
		<p>test</p>
	</body>
</html>
```

### 引用 css

css 内容

```css
div {
	width: 100%;
	height: 100px;
	font-size: 50px;
}
```

html 内容

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Document</title>
		<link
			rel="stylesheet"
			type="text/css"
			href="test.css"
		/>
	</head>
	<body>
		<div>test</div>
	</body>
</html>
```

最后思考为何要把 css 放在 head 中？？？ 减少重复渲染

### 引入 js

js 内容

```js
document.getElementById("container").innerHTML = "update by js";
```

html 内容

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Document</title>
	</head>
	<body>
		<div id="container">default</div>
		<script src="index.js"></script>
		<p>test</p>
	</body>
</html>
```

思考为何要把 JS 放在 body 最后？？？ 先渲染再修改

### 有图片：不会等图片，继续向下渲染

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Document</title>
	</head>
	<body>
		<p>test</p>
		<p><img src="test.png" /></p>
		<p>test</p>
	</body>
</html>
```

引出`window.onload`和`DOMContentLoaded`

```js
window.addEventListener("load", function () {
	// 页面的全部资源加载完才会执行，包括图片、视频等
});
document.addEventListener("DOMContentLoaded", function () {
	// DOM 渲染完即可执行，此时图片、视频还可能没有加载完
});
```

<hr style="border: 10px solid green" />

# 解答

## 从输入 url 到得到 html 的详细过程

- 浏览器根据 DNS 服务器得到域名的 IP 地址
- 向这个 IP 的机器发送 http 请求
- 服务器收到、处理并返回 http 请求
- 浏览器得到返回内容

## window.onload 和 DOMContentLoaded 的区别

- 页面的全部资源加载完才会执行，包括图片、视频等
- DOM 渲染完即可执行，此时图片、视频还没有加载完
