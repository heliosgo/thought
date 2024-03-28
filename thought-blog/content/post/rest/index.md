+++
title = 'RESTful 服务'
date = 2024-03-27T22:53:36+08:00
draft = false
+++

# RESTful 服务

## 基本概念

> 维基百科: https://zh.wikipedia.org/wiki/%E8%A1%A8%E7%8E%B0%E5%B1%82%E7%8A%B6%E6%80%81%E8%BD%AC%E6%8D%A2

表现层状态转换（英语：Representational State Transfer，缩写：REST）是 Roy Thomas Fielding
博士于2000年在他的博士论文中提出来的一种万维网软件架构风格，目的是便于不同软件/程序在网络
（例如互联网）中互相传递信息。表现层状态转换是根基于超文本传输协议（HTTP）之上而确定的一组约束和属性，
是一种设计提供万维网络服务的软件构建风格。符合或兼容于这种架构风格（简称为 REST 或 RESTful）
的网络服务，允许客户端发出以统一资源标识符访问和操作网络资源的请求，而与预先定义好的无状态操作集一致化。
因此表现层状态转换提供了在互联网络的计算系统之间，彼此资源可交互使用的协作性质（interoperability）。

- 资源(Resource)

当你在阅读这篇文章时候，文章内容本身就是一种资源，不管是在电脑，还是手机上，尽管看到的表现形式不同，但看到的都是同一篇文章。

- 表征(Representation)

表征其实就是表现形式，表示出来的样子，就像在浏览器上请求阅读这篇文章的时候，你拿到的是服务器返回的
HTML，或者将这篇文章打印出来，拿到的就是一张纸。这些都是同一个资源的多种表征。

- 状态(State)

在阅读文章时，通常会有下一篇，上一篇的按钮。如果我们想要向服务器请求下一篇文章，那么服务器就要知道当前文章，因为“下一篇”是一个相对的
概念，状态不是一个独立的概念，而是基于一个语境，某一时刻的样子。

状态又分为有状态(Stateful)和无状态(Stateless)，都是相对于服务端来说的。服务端记住状态就是有状态，客户端记住状态就是无状态。
而 REST 其中的一个原则就是无状态原则。

- 转移(Transfer)

不管是有状态还是无状态，获取下一篇文章这个动作都只能由服务端来完成。服务器通过某种方式，把“用户当前阅读的文章”转变成“下一篇文章”，
这就被称为“表征状态转移”。表征状态转移是由浏览器主动向服务器请求引发的，该请求导致了“在屏幕上显示出了下一篇文章的内容”。但这不是
浏览器的意图，浏览器是通过服务器响应的超文本来驱动状态转移的。


## REST 的六个原则

- 服务端与客户端分离(Client-Server)  
用户界面关注点和数据存储关注点的分离可以提高用户界面在多个平台的可移植性。

- 无状态(Stateless)  
REST
希望从客户端发出的每个请求都必须包含所有的信息，服务端不能保存客户端的任何上下文信息，服务端通过客户端发送的状态信息来进行业务处理。

- 可缓存(Cacheability)  
因为无状态的特性，为完成某个功能可能就需要多次请求，或者在请求中携带冗余的信息。为了缓解该情况，服务端的响应必须明确地或隐含地定义自身
是否可以被缓存。如果是响应是可缓存的，那么客户端缓存可以重用以前的响应以改进效率和可伸缩性。

- 统一接口(Uniform Interface)  
为了得到统一的接口，REST
希望开发者面向资源编程，把软件系统设计的重点放在抽象系统该有哪些资源上，而不是抽象系统有哪些行为上。

- 分层系统(Layered System)  
这里所指的分层并不是表示层、服务层、持久层这种意义上的分层，而是指客户端不能知道它是与最终服务器还是中间服务器进行交互。
中间服务器可以提供负载均衡和共享缓存的机制，提高系统的可扩展性。

- 按需代码(Code-On-Demand)  
按需代码是一条可选的原则，指的是任何按照客户端的请求，将可执行的软件程序从服务器发送到客户端的技术。比如 JavaScript。

## 什么样的服务才能叫 RESTful？

Leonard Richardson 曾经提出过一个 REST
服务成熟度模型，在这个模型里，从低到高分为 0 至 3 共 4 级：

1. The Swamp of POX：完全不 REST。
2. Resources：开始引入资源的概念。
3. HTTP Verbs：引入统一接口，映射到 HTTP 协议的方法上。
4. Hypermedia Controls：超文本驱动。

![](overview.png)

Martin Fowler 在一篇文章中通过一个实际例子来描述了在该模型下，实际的 API
是怎样的。

### Level 0: The Swamp of Plain Old XML

当要去医院看病时，想要知道医生的空闲时段，医院的预约软件上提供了一个
/appointmentService
接口，通过传入日期、医生姓名作为参数，就可以得到医生在该日期下的空闲时间。

```json
POST /appointmentService HTTP/1.1

{
    date: "20100104", 
    docter: "mjones"
}
```
服务器收到请求后，就会返回所需要结果。
```json
HTTP/1.1 200 OK

[
    {
        start: "1400", 
        end:"1450", 
        docter: "mjones"
    },
    {
        start: "1600", 
        end:"1650", 
        docter: "mjones"
    }
]

```
下一步是预约
```json
POST /appointmentService?action=confirm HTTP/1.1

{
    appointment: {
        date: "20100104", 
        start: "1400", 
        end: "1450", 
        docter: "mjones"
    },
    patient: {
        name: "tom"
    }
}
```
如果一切正常，就会收到一个预约成功的回复
```json
HTTP/1.1 200 OK

{
    code: 0, 
    message: "预约成功"
}
```
如果出现了问题，比如说其他人在我之前预约，就会收到相应的错误信息
```json
HTTP/1.1 200 OK

{
    code: 1, 
    message: "预约失败，医生预约已满"
}
```
这是一个很简单的基于 RPC 风格设计。

### Level 1: Resources
实现 REST 的第一步就是要引入资源的概念，所有操作都围绕资源来进行。
```json
POST /docter/mjones HTTP/1.1

{
    date: "2010-01-04"
}
```
然后，服务器返回一个包含 ID 的信息，因为医生的档期被视为一种资源。
```json
HTTP/1.1 200 OK

[
    {
        id: 1234, 
        start: "1400", 
        end:"1450", 
        docter: "mjones"
    },
    {
        id: 5678, 
        start: "1600", 
        end:"1650", 
        docter: "mjones"
    }
]
```
我觉得 14:00 到 14:50 比较合适
```json
POST /slots/1234 HTTP/1.1

{
    name: "tom"
}
```
如果一切顺利，那么同样会收到和之前类似的答复。
```json
HTTP/1.1 200 OK

{
    code: 0, 
    message: "预约成功"
}
```

### Level 2: HTTP Verbs
在 Level 0 和 Level 1 所有的交互中都是用了 HTTP POST，但在 Level
2 中不会这么做，而是尽可能地用 HTTP 本身的方式。
对于档期，我们会使用具有查询语义的 GET。
```json
GET /doctors/mjones/slots?date=20100104&status=open HTTP/1.1
```
服务器答复和之前相同。
```json
HTTP/1.1 200 OK

[
    {
        id: 1234, 
        start: "1400", 
        end:"1450", 
        docter: "mjones",
    },
    {
        id: 5678, 
        start: "1600", 
        end:"1650", 
        docter: "mjones"
    }
]
```
然后我们需要一个可以改变状态的动词，在该场景下 POST 是更符合语义的。
```json
POST /slots/1234 HTTP/1.1

{
    name: "tom"
}
```
如果一切顺利，服务将回复响应代码 201，以表明有新的资源。
```json
HTTP/1.1 201 Created
```
如果失败，比如有人已经预约。
```json
HTTP/1.1 409 Conflict
```
目前绝大多数的系统都能达到 Level
2。不过这种方案还不够完美，最主要一个问题是：我们如何知道预约 mjones
医生的档期，需要访问 "/slots/1234" 这个接口。

### Level 3: Hypermedia Controls

REST
认为除了第一个请求外，后续的请求都应该能够自己描述清楚可能发生的状态转移，由超文本自身来驱动。  
所以当第一个请求发生后
```json
GET /doctors/mjones/slots?date=20100104&status=open HTTP/1.1
```
响应里应该包括如何预约医生等操作。
```json
HTTP/1.1 200 OK

[
    {
        id: 1234, 
        start: "1400", 
        end:"1450", 
        docter: "mjones",
        links: "/slots/1234"
    },
    {
        id: 5678, 
        start: "1600", 
        end:"1650", 
        docter: "mjones",
        links: "/slots/5678"
    }
]
```
这样客户端就可以根据返回的响应，来决定自己的行为，实现后端模型驱动。


