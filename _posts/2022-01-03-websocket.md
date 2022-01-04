---
layout:     post
title:      "websocket"
subtitle:   "简单了解学习一下websocket的使用"
date:       2022-01-03 20:00:00
author:     "ddxg"
header-img: "img/post-bg-nextgen-web-pwa.jpg"
header-mask: 0.3
catalog:    true
tags:
    - websocket
---

# webSocket

## 什么是websocket？
> WebSocket是HTML5新增的双向通信协议，它建立在TCP之上，它的目的是在浏览器和服务器之间建立一个不受限的双向通信的通道，可以向服务器发送消息并接收事件驱动的响应，而无需通过轮询服务器的方式以获得响应。

浏览器和服务器只需要做一个握手的动作，然后浏览器和服务端之间就形成了一条快速通道，两者之间就直接可以数据相互传送。在海量并发及客户端与服务器交互负载流量大的情况下，极大的节省了网络带宽资源的消耗，有明显的性能优势，且客户端发送和接受消息是在同一个持久连接上发起，实时性优势明显。

后端需要主动给前端发消息的业务、前后端交互频繁或者对数据实时性要求高的页面比较适合使用 `webSocket` 实现。比如，聊天室、web端游戏、数据大屏等。

![webSocket通信](/img/2022/ws-2.png)


当然也可以使用轮询来实现，可能会有一些缺陷。

## 轮询

> 主要有 传统Ajax短轮询、Comet技术 两种。

下图左边的是 短轮询，右边的是 HTTP长轮询（Comet技术）

![传统Ajax短轮询与Comet技术的区别](/img/2022/ws-1.png)

### 传统Ajax短轮询的缺点
轮询是指浏览器通过JavaScript启动一个定时器，然后以固定的间隔给服务器发请求，询问服务器有没有新消息。这个机制的缺点一是实时性不够，二是频繁的请求会给服务器带来极大的压力。

### HTTP长轮询(Comet技术)的缺点
Comet本质上也是轮询，但是在没有消息的情况下，服务器先拖一段时间，等到有消息了再回复。这个机制暂时地解决了实时性问题，但是它带来了新的问题：以多线程模式运行的服务器会让大部分线程大部分时间都处于挂起状态，极大地浪费服务器资源。另外，一个HTTP连接在长时间没有数据传输的情况下，链路上的任何一个网关都可能关闭这个连接，而网关是我们不可控的，这就要求Comet连接必须定期发一些ping数据表示连接“正常工作”。


对于功能简单，时间周期短的功能，使用短轮询还是合适的，比如，扫码登陆、扫码支付等。对实时性和性能要求更高的页面，还是拥抱 `WebSocket` 吧。


## `WebSocket` 协议
WebSocket并不是全新的协议，而是利用了HTTP协议来建立连接。

WebSocket与http协议一样都是基于TCP的，所以他们都是可靠的协议，调用的WebSocket的send函数在实现中最终都是通过TCP的系统接口进行传输的。

WebSocket和Http协议一样都属于应用层的协议，WebSocket在建立握手连接时，数据是通过http协议传输的，但是在建立连接之后，真正的数据传输阶段是不需要http协议参与的。

## `WebSocket` 连接流程
`WebSocket` 连接必须由客户端发起

### 客户端发起`WebSocket` 连接请求

`new WebSocket` 创建一个WebSocket对象，发起一个HTTP请求，请求头如下：

![传统Ajax短轮询与Comet技术的区别](/img/2022/ws-3.png)

与常见的HTTP请求有几处不同：
- 请求url的协议是 `ws://`。
- 请求头 `Upgrade: websocket;Connection: Upgrade` 表示这个连接将要被转换为WebSocket连接。
- `Sec-WebSocket-Key`: 一个 base64编码的密文，与响应头中的 `Sec-WebSocket-Accept` 相呼应。
- `Sec-WebSocket-Version`: WebSocket的协议版本

### 服务端响应

![传统Ajax短轮询与Comet技术的区别](/img/2022/ws-4.png)

- `101 Switching Protocols`: 可以修改协议
- `Upgrade: websocket` 修改为 `WebSocket`

`WebSocket` 连接成功

### `WebSocket` 通信

连接成功之后，客户端和服务端可以进行相互同行了。在控制台可以查看通信数据。图中红颜色代表服务端推送，绿色是客户端的请求。

通信数据有两种，文本和二进制。页面数据习惯会使用 `json` 字符串表示。

![传统Ajax短轮询与Comet技术的区别](/img/2022/ws-5.png)


## `WebSocket` 的使用（客户端）
用法还是比较简单的，[直接看MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)

`WebSocket` 有4个常量，6个属性，2个方法，4个事件。

直接看效果

## `WebSocket` 相关的库
- [Socket.io](https://socketio.bootcss.com/docs/)，客户端和服务端都有
- [ws](https://github.com/websockets/ws)，nodejs的库

## 参考
- [websocket-MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)
- [廖雪峰-websocket](https://www.liaoxuefeng.com/wiki/1022910821149312/1103303693824096)
- [web服务端推送技术简介](https://blog.csdn.net/u010663691/article/details/108664344)
- [WebSocket协议-基础篇](https://www.jianshu.com/p/1b2019b02126)
- [Socket.is](https://www.jianshu.com/p/4e80b931cdea)

