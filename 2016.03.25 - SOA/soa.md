title: 消息推送整合平台
speaker: 王奥丞, 黄锐皓, 韦毅龙
url: http://soappt.a1ex.wang
transition: cards
files: /js/demo.js,/css/demo.css

[slide]
# 消息推送整合平台
## - 王奥丞, 黄锐皓, 韦毅龙

[slide]
## 需求
------
- 现实生活中消息推送种类很多, 经常容易忘记 {:&.rollIn}
- 做一个统一的消息推送接口
- 可以从不同的数据源获取通知(网络学堂,科协服务)
- 可以添加不同的前端(app通知, email通知)

[slide]
## 整体结构
------
- ![Architecture](/assets/arch.png) {:&.rollIn}
- Connect once. Notify everywhere!

[slide]
## 后端
------
- 后端指消息的来源 {:&.rollIn}
- 可以是...
- 网络学堂
- 科协net9系列服务
  - resource9, xxx推送了一条消息 {:&.rollIn}
  - download9, xx种子下载完毕
  - git9, xxx给我了一个star
- 邮箱: outlook, gmail, 163...把邮件当成一种消息推送
- 社交网站:
  - github  {:&.rollIn}
  - stackoverflow...
- 甚至是其他同学的项目

[slide]
## 前端
------
- 前端指给用户呈现消息的程序 {:&.rollIn}
- 手机端推送: Android App..
- 网页版
- 邮件通知: outlook, gmail, 163...
- 添加到Google Calendar
- ...

[slide]
## 优点
------
- 可扩展 {:&.rollIn}
  - 易添加新的后端, 前端 {:&.rollIn}
  - 前后端解耦, 可任意组装, 甚至后端可以成为前端
- 可定制
  - 可以筛选要从哪些后端获取数据 {:&.rollIn}
  - 可以添加一些自定义消息分类的机制, 比如正则表达式匹配等

[slide]
## 实现细节
------
- 前端使用长轮询获得消息, 减少服务器压力 {:&.rollIn}
- 最小化给前端提供的api, 比如只有查询消息,设为已读, 使前端开发容易
- 将复杂的逻辑(比如消息过滤)封装在scheduler(前图)中, 用户可定制scheduler

[slide]

# That's all, thank you!
