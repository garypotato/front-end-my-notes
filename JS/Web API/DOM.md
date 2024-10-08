# DOM 知识点

---

## DOM 的本质

讲 DOM 先从 html 讲起，讲 html 先从 XML 讲起。XML 是一种可扩展的标记语言，所谓可扩展就是它可以描述任何结构化的数据，它是一棵树！

```xml
<?xml version="1.0" encoding="UTF-8"?>
<note>
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
  <other>
    <a></a>
    <b></b>
  </other>
</note>
```

HTML 是一个有既定标签标准的 XML 格式，标签的名字、层级关系和属性，都被标准化（否则浏览器无法解析）。同样，它也是一棵树。

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Document</title>
	</head>
	<body>
		<div>
			<p>this is p</p>
		</div>
	</body>
</html>
```

我们开发完的 html 代码会保存到一个文档中（一般以`.html`或者`.htm`结尾），文档放在服务器上，浏览器请求服务器，这个文档被返回。因此，最终浏览器拿到的是一个文档而已，文档的内容就是 html 格式的代码。

但是浏览器要把这个文档中的 html 按照标准渲染成一个页面，此时浏览器就需要将这堆代码处理成自己能理解的东西，也得处理成 JS 能理解的东西，因为还得允许 JS 修改页面内容呢。

基于以上需求，浏览器就需要把 html 转变成 DOM，html 是一棵树，DOM 也是一棵树。对 DOM 的理解，可以暂时先抛开浏览器的内部因此，先从 JS 着手，即可以认为 DOM 就是 JS 能识别的 html 结构，一个普通的 JS 对象或者数组。

【附带一个 chrome Element 的截图】

---

## DOM 节点操作

### 获取 DOM 节点

```javascript
const div1 = document.getElementById('div1'); // 元素
const divList = document.getElementsByTagName('div'); // 集合
console.log(divList.length);
console.log(divList[0]);

const containerList = document.getElementsByClassName('.container'); // 集合
const pList = document.querySelectorAll('p'); // 集合
```

### prototype

DOM 节点就是一个 JS 对象，它符合之前讲述的对象的特征 ———— 可扩展属性

```javascript
const pList = document.querySelectorAll('p');
const p = pList[0];
console.log(p.style.width); // 获取样式
p.style.width = '100px'; // 修改样式
console.log(p.className); // 获取 class
p.className = 'p1'; // 修改 class

// 获取 nodeName 和 nodeType
console.log(p.nodeName);
console.log(p.nodeType);
```

### Attribute

property 的获取和修改，是直接改变 JS 对象，而 Attibute 是直接改变 html 的属性。两种有很大的区别

```javascript
const pList = document.querySelectorAll('p');
const p = pList[0];
p.getAttribute('data-name');
p.setAttribute('data-name', 'imooc');
p.getAttribute('style');
p.setAttribute('style', 'font-size:30px;');
```

### 总结

- 都会导致重渲染
- 尽量使用 prototype 去操作

---

## DOM 树操作

新增节点

```javascript
const div1 = document.getElementById('div1');
// 添加新节点
const p1 = document.createElement('p');
p1.innerHTML = 'this is p1';
div1.appendChild(p1); // 添加新创建的元素
// 移动已有节点。注意是移动！！！
const p2 = document.getElementById('p2');
div1.appendChild(p2);
```

获取父元素

```javascript
const div1 = document.getElementById('div1');
const parent = div1.parentNode;
```

获取子元素

```javascript
const div1 = document.getElementById('div1');
const child = div1.childNodes;
```

删除节点

```javascript
const div1 = document.getElementById('div1');
const child = div1.childNodes;
div1.removeChild(child[0]);
```

还有其他操作的 API，例如获取前一个节点、获取后一个节点等，但是面试过程中经常考到的就是上面几个。

---

## DOM 性能

DOM 操作是昂贵的 —— 非常耗费性能。因此针对频繁的 DOM 操作一定要做一些处理。

例如缓存 DOM 查询结果

```js
// 不缓存 DOM 查询结果
for (let = 0; i < document.getElementsByTagName('p').length; i++) {
	// 每次循环，都会计算 length ，频繁进行 DOM 查询
}

// 缓存 DOM 查询结果
const pList = document.getElementsByTagName('p');
const length = pList.length;
for (let i = 0; i < length; i++) {
	// 缓存 length ，只进行一次 DOM 查询
}
```

再例如，插入多个标签时，先插入 Fragment 然后再统一插入 DOM

```js
const listNode = document.getElementById('list');

// 创建一个文档片段，此时还没有插入到 DOM 树中
const frag = document.createDocumentFragment();

// 执行插入
for (let x = 0; x < 10; x++) {
	const li = document.createElement('li');
	li.innerHTML = 'List item ' + x;
	frag.appendChild(li);
}

// 都完成之后，再插入到 DOM 树中
listNode.appendChild(frag);
```

<hr style="border: 10px solid green" />

# DOM 解答

## DOM 是哪种基本的数据结构？

树

## DOM 操作的常用 API 有哪些

- 获取节点，以及获取节点的 Attribute 和 property
- 获取父节点 获取子节点
- 新增节点，删除节点

## DOM 节点的 attr 和 property 有何区别

- property 只是一个 JS 属性的修改
- attr 是对 html 标签属性的修改

## 如何一次性插入多个 DOM 节点，考虑性能

- 使用 fragment
- 先插入节点
- 最后再插入 DOM 树
