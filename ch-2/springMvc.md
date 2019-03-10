# SpringMvc

[【源码仓库】](https://github.com/wt1187982580/javaBook-src/tree/master/mySpringMvcBook)

## 三层结构

- 表现层
  - MVC模型

- 业务层
  - service 

- 持久层
  - dao

## 工作流程

```sequence
用户->前端控制器:用户发送请求
前端控制器-> 后端控制器:根据用户请求查询具体控制器
后端控制器-->前端控制器:处理后结果
前端控制器--> 视图:视图渲染
视图-->前端控制器:返回视图
前端控制器--> 用户:响应结果

```