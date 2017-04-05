[TOC]

## Restful API 规约

### 与传统HTTP的区别

REST架构基于标准HTTP方法，包括常用的GET、POST、PUT、PATCH、DELETE、HEAD、OPTIONS等等，将每个用户需求抽象成资源，任何对资源的操作都通过HTTP协议完成，在整个URI中不能出现动词，形如：http://api.muarine.com/demo/persons/zhangsan，对比于传统的URI http://api.muarine.com/demo/persons?name=zhangsan，将用户需求和约束放在URI中

### 域名规范

对外提供的服务做一个解释说明，一般独占二级域名api，精简URI , 形如：http://api.muarine.com/demo/persons 或者 http://www.muarine.com/api/demo/persons

###  HTTP 方法实际应用

GET：访问某个资源单元或者集合，在URI中就做出体现，形如：http://api.muarine.com/demo/persons，http://api.muarine.com/demo/persons/zhangsan

POST：添加目标资源

PUT：全量覆盖更新目标资源

PATCH：局部更新目标资源

DELETE：删除目标资源

HEAD：少见，描述目标资源的元数据信息并放置在响应的报头集合返回给客户端，一般没有主体响应内容

OPTIONS：少见，发送某种“探测”请求来确定目标地址是否有效，比如针对“跨域资源”的预检（Preflight）请求