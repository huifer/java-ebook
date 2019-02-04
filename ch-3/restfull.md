# restful



三个概念

1. Resource : 资源， 主要指数据
2. Representational:数据表现形式 json xml
3. State Transfer ： 状态变化 HTTP method描述

支持操作

1. GET:从服务器上获取资源
2. POST:创建新的资源
3. PUT:更新服务器资源
4. DELETE:删除服务器资源

返回码

1. 2xx : 请求正常处理并返回
2. 3xx : 重定向，请求资源位置发生变化
3. 4XX : 客户端发送的请求错误
4. 5xx : 服务端错误

URL路径规范

1. 使用名词复数形式
2. 单词之间用_分割
3. 实例
   1. 获取所有用户 GET /api/users?page=1&size=20
   2. 获取单一用户 GET /api/users/{user_id}
   3. 创建用户 POST /api/users
   4. 删除用户 DELETE /api/users/{user_id}
   5. 更新用户 PUT /api/users/{user_id}