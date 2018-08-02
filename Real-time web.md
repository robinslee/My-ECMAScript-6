# Real Time Web

实时Web，字面意思就是浏览器和服务器之间可以不间断、近乎无延迟的互相传输数据, 而传统Web的工作方式基于请求和响应, 即浏览器发送一次请求, 服务器响应一份数据. 在传统Web当中, 浏览器无法感知服务器状态的变化, 所以要么定时轮询发送请求, 要么干脆等用户不断刷新来获取最新数据.

为什么实时Web?

在实时Web当中, 客户端和服务器端直接打开一条连接, 进而相互发消息, 无需任何请求和等待, 是一种全双工通信方式. 所以服务端有变化, 直接发送数据给客户端, 客户端任何变化也直接发送给服务器, 服务器有权决定是否将这个变化通知其他客户端.

传统Web的通信模型是半双工的, 客户端是一个主动的角色, 服务器端是一个被动的角色. 客户端主动推数据给服务端(发送请求), 服务端基于请求推数据给客户端(响应请求), 而后等待下一次请求, 直到连接关闭
在建立好TCP连接之后, 客户端可以主动

## Solutions
* Polling (AJAX, meta refresh, tag src, fetch)
    * Short polling
    * Long polling
* Long Connection
* Web Socket (Socket.io)
* HTTP/2 Server push
* Flash, Applet, SilverlightComet
* Server Side Event

## Scenarios
* Live Chat(IM), Notifications, Stock exchange
* Online game
* Dashboard
* IoT control & monitoring



是否想过把实时传感器数据，可以在浏览器上显示？怎样在网页上实时的控制和监控嵌入式设备？答案是WebSocket可以做到!在这篇文章将简单介绍一下WebSocket, 它如何工作, 它的好处, 最后介绍如系统监控、仪表盘应用的开发.

1. 什么是WebSocket
    WebSocket是客户端和服务端之间建立的, 低延迟/全双工/长时的一条连接. WebSockets为实时/事件驱动的web应用带来巨大好处, 而且正在变得更
2. 显示开关状态, Javascript和WebSockets
3. WebSocket安全
4. 总结