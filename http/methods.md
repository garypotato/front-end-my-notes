# http methods

---

## 传统的 methods

- get 获取服务器的数据
- post 向服务器提交数据
- 简单的网页功能，就这两个操作

---

## 现在的 methods

- get 获取数据
- post 新建数据
- patch/put 更新数据
- delete 删除数据

---

## Restful API

- 传统 API 设计： 把每个 url 当做一个功能
- Restful API 设计： 把每个 url 当做一个唯一的资源

### 不使用 url 参数

- 传统 API 设计：/api/list?pageIndex=2
- Restful API 设计： /api/list/2

### 用 method 表示操作类型

- 传统 API
  - post 请求：/api/create-blog
  - post 请求: /api/update-blog?id=100
  - get 请求： /api/get-blog?id=100
- Restful API
  - post 请求: /api/blog
  - patch 请求: /api/blog/100
  - get 请求: /api/blog/100
