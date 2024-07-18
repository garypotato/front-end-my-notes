# http headers

---

## 常见的 Request Headers

- accept 浏览器可接收的数据格式
- accept-Encoding 浏览器可接收的压缩算法，如 gzip
- accept-Language 浏览器可接收的语言，如 zh-CN
- Connection: keep-alive 一次 TCP 连接重复使用
- cookie
- Host
- User-Agent 浏览器信息
- Content-type 发送数据的格式，如 application/json

---

## 常见的 Respond Headers

- Content-type 返回数据的格式，如 application/json
- Content-length 返回数据的大小，多少字节
- Content-Encoding 返回数据的压缩算法，如 gzip
- Set-Cookie

---

## 自定义 header

headers: {'X-Requested-With': 'XMLHttpRequest'}

---

## 缓存相关的 headers

- Cache-Control: Expires
- Last-Modified: If-Modified-Since
- Etag: If-None-Match
