# CQBot-swoole 开发文档
<br>

[CQBot-swoole](https://github.com/crazywhalecc/CQBot-swoole)是一个异步、高性能、多账号的CQ-HTTP-API插件框架。


## 简介
CQBot-swoole虽然是用PHP写的，但因为使用了Swoole扩展，故使用方式和传统的PHP有很多不同。

因为本项目是基于[CoolQ HTTP API插件](https://cqhttp.cc/)而开发的SDK，所以一些API、以及插件的配置文件和事件上报格式需要通过插件提供的官方文档进行查阅，本文档不作重复。

文档内涵盖了整个框架的结构、模块结构以及其他特性的介绍。如果你已经安装好了本项目，并且已经成功地在群内提示了机器人已连接，那么说明你的框架部署成功，可以开始开发了。

## 注意事项
因为框架采用的是Swoole的方式来运作的，所以在运行后项目会开启多个子进程，详见[Swoole官方文档](https://wiki.swoole.com/wiki/page/163.html)。项目默认的设置是开启了一个工作进程（处理事件和API的功能代码部分），故不必过于担心多进程造成的一些问题。

框架结构默认采用单进程+异步非阻塞方式进行事件处理，支持多个机器人同时连接框架，请不要在事件处理回调中使用过多的阻塞函数。Swoole提供的异步API已经非常完善，本框架也进行了一些简单的封装供快速使用。

框架第一次启动后，会启动引导模式，用于初始化监听的地址、端口、设置机器人总管理群和主人qq以及和cqhttp插件约定的access token等。启动完成后，当看到```===== Worker #0 已启动 =====```的时候说明启动已经完成，等待cqhttp插件的```反向ws```的连接。

如果你有一台公网IP的服务器但是框架和酷Q恰巧不在一台服务器上，可以使用[frp](https://github.com/fatedier/frp)进行内网穿透或将框架放到有公网IP的服务器上。

如果因代码逻辑造成的卡死或循环报错，可以使用Ctrl+C中止框架的运行，并到```cq_data/crash```文件夹下查看报错追踪日志。