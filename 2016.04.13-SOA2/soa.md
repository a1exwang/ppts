title: 消息整合服务
speaker: 王奥丞, 黄锐皓, 韦毅龙
url: https://ppt.a1ex.wang
transition: cards
files: /js/demo.js,/css/demo.css

[slide]
# 消息推送整合平台
## - 王奥丞, 黄锐皓, 韦毅龙

[slide]
# 需求
------
- 做一个统一的消息管理App {:&.rollIn}
- 可以从不同的数据源获取消息
- 可以推送到不同的前端
- 在一个App登陆可以收到不同来源的消息

[slide]
## 整体结构
------
![Architecture](/assets/main.jpg)  {:&.rollIn}

[slide]
## 两种方式
------
![Poll-Push](/assets/poll-push.jpg)  {:&.rollIn}

[slide]
## 轮询
------
- 用于两台服务器或者一台服务器和客户端之间  {:&.rollIn}
- 存消息的服务器为Server, 获取消息的是Client
- GET     /newMessages   # 获取未读消息
- UPDATE  /newMessage/id # 设置消息为已读
- DELETE  /newMessage/id # 删除消息
- GET     /messages      # 获取所有历史消息

[slide]
## 推送
------
- 用于两台服务器之间, 提高性能  {:&.rollIn}
- POST /message, 向该服务器推送消息

[slide]
## 每台服务器储存的数据
------
- ![Auth](/assets/auth.jpg)  {:&.rollIn}
- Adapter, Scheduler

[slide]
## 序列图
------
![Seq](/assets/seq.jpg)  {:&.rollIn}

[slide]
## Server具体架构
------
- 总共n台adapter, 1台scheduler, 1台authenticaten server  {:&.rollIn}
- JSON API Server使用sinatra...
- 数据库: SQL
- 两种访问模式: 轮询, 推送

[slide]
## JSON Schema
------
- messageList  {:&.rollIn}
  - url, id, token  {:&.rollIn}
- message
  - type:  {:&.rollIn}
  - id
  - url
  - title
  - content
  - timestamp

[slide]
## 备注
------
- adapter需要缓存所有消息, 记录消息状态(包括已删除, 否则不能直到是否是新推送的消息)  {:&.rollIn}
- adapter中可以加缓存
- scheduler中只保存每个用户的新消息id和消息id
- 成环检测?
- scalability

[slide]
## 计划
- 第十周~第十四周
- 第十周周: 网络学堂, scheduler, 命令行客户端(确定稳定的接口)
- 第十一周: 用户系统
- 第十二周一周: RSS, POP3/IMAP Adapter
- 第十一,十二周: 安卓+HTML客户端
- 第十三周: 部署+测试
- 第是四周: 完成

[slide]
# That's all, thank you!  {:&.rollIn}
