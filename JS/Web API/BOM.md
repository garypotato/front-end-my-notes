# BOM 知识点

DOM 是浏览器针对下载的 HTML 代码进行解析得到的 JS 可识别的数据对象。而 BOM（浏览器对象模型）是浏览器本身的一些信息的设置和获取，例如获取浏览器的宽度、高度，设置让浏览器跳转到哪个地址。

- navigator
- screen
- location
- history

这些对象就是一堆非常简单粗暴的 API 没人任何技术含量，讲起来一点意思都没有，大家去 MDN 或者 w3school 这种网站一查就都明白了。面试的时候，面试官基本不会出太多这方面的题目，因为只要基础知识过关了，这些 API 即便你记不住，上网一查也都知道了。

```javascript
// navigator 浏览器信息
var ua = navigator.userAgent;
var isChrome = ua.indexOf('Chrome');
console.log(isChrome);

// screen
console.log(screen.width);
console.log(screen.height);

// location
console.log(location.href);
console.log(location.protocol); // 'http:' 'https:'
console.log(location.pathname); // '/learn/199'
console.log(location.search);
console.log(location.hash);

// history
history.back();
history.forward();
```

<hr style="border: 10px solid green" />

# BOM 解答

## 如何检测浏览器的类型

```javascript
var ua = navigator.userAgent;
var isChrome = ua.indexOf('Chrome');
console.log(isChrome);
```

## 拆解 url 的各部分

```javascript
console.log(location.href);
console.log(location.protocol); // 'http:' 'https:'
console.log(location.pathname); // '/learn/199'
console.log(location.search);
console.log(location.hash);
```
