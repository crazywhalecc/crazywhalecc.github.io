# 框架运行机制

此页用于说明框架的运行机制和CQBot-swoole所采用的websocket server运行方式。

> CQBot-swoole采用的是反向ws

## 什么是反向ws、正向ws、HTTP？
我们知道，HTTPAPI插件提供了三种模式来上报事件和接收API请求，其中**默认开启**的是HTTP模式。
在这里，**HTTP模式**的工作方式大致可以写成这个样子：

- HTTPAPI插件是一个服务器，设置host、port为`0.0.0.0:5700`，这是它接收API请求的端口。
- `post_url`为上报事件的地址，比如你采用传统PHP方式写接收处理事件的代码，则应该把PHP代码放入nginx或Apache等Web服务器的目录下，然后像平常一样请求php文件即可上报。
- 这时候你在你的PHP代码中处理完你的逻辑代码需要发送API请求时候，你只需在PHP代码中发起HTTP请求给上面HTTPAPI插件所监听的host和port即可发送API。
- 请求API后会直接返回[状态包响应](https://cqhttp.cc/docs/#/API?id=响应说明)，如果需要使用API请求的结果继续写业务代码，则顺序接着写就可以。

> HTTP模式适用于并发量较小、测试环境、业务代码和HTTPAPI插件运行在一台机器上。

接下来是**正向WebSocket模式**的工作方式：

- HTTPAPI插件在启用正向Websocket时候，会开启一个WS服务器，假设监听的地址和端口为`0.0.0.0:10086`。
- 你使用node.js、PHP-swoole、python、Chrome浏览器或其他带有ws客户端功能的语言或工具连接到HTTPAPI插件。你可以使用多个客户端连接一个HTTPAPI插件。
- 当连接后，HTTPAPI插件会通过这条连接给你推送事件（如好友、群组发来的消息）
- 接收事件后你处理你的业务逻辑代码
- 上报API。这时假设你需要使用API请求后返回的结果（如`get_status`），这时你的业务逻辑代码不能直接像HTTP一样接收请求后的内容了。
- 在使用websocket推送(push)了API请求后，HTTPAPI插件会在之后返回给你结果，但你直接请求的话你的业务代码并不知道哪个究竟是你想要返回的包。
- 这时候你需要使用`echo`标记以下你发出去的API。就好比给信鸽腿上绑序号一样，这样你就能确定飞回来的鸽子究竟是来自John还是Mike了。
- 比如你使a消息的echo参数等于12345，等收到回包时，你只需判断回包是否存在echo字段并且此echo字段你有对应保存了内容。
- 在判断并找到对应echo回包后，你继续写剩下的业务代码。（例如get_status后给管理员上报里面的酷Q状态）

> 正向WS模式适用于并发量较大、需要将事件包上报到多处、或业务逻辑代码运行在另一台没有公网IP的机器上。

> 正向ws简单来说是一对多方式（一个QQ对多个客户端）

其中**反向WebSocket模式**工作方式和正向WS很类似

- 反向WS模式就是你自己使用node.js、PHP-swoole、python或其他带有ws服务端给你的语言或工具创建一个WebSocket服务器，假设监听端口为`0.0.0.0:10086`。
- 然后HTTPAPI插件设置反向ws连接地址后，就可以连接到你的ws服务器进行通信了。
- 剩下的方式和正向ws中叙述的一样。因为websocket是双向的。作为服务器可以主动给客户端发消息，客户端也可以同时给服务端发消息。

> 反向WS模式适用于并发量较大、同一业务代码处理多个QQ、或业务逻辑代码运行在有公网IP且酷Q运行在没有公网IP的机器上。

> 反向ws简单来说是多对一方式（多个QQ对一个服务端）


代码复杂性上考虑，同步的`HTTP模式`下写SDK、框架或功能代码是最简单的，但在多机器人支持上`反向WebSocket`更合适。如果你只有一个机器人但同时需要多个客户端去处理消息（例如一边是业务代码，一边是web控制台），显然`正向ws`更合适。

上面也有可能我表述得不是很好，所以没理解的话，可以通过**搜索引擎**去学习相关的知识。

## 框架的运行流程

- `start.php` 初始化实例
- `Framework.php` 初始化Swoole WebSocket Server
- start后Worker进程初始化完成后激活`WorkerStartEvent.php`。

#### 消息事件处理流程
- 接收到的事件上报`WSMessageEvent.php`
- 消息事件将实例化对象`CrazyBot.php`并将message分发到每个mods下的模块
- CrazyBot.php处理要请求的API，使用`CQAPI.php`
- 如果需要使用API请求后返回的结果，则使用闭包回调函数。
- 框架将给请求的API包内使用echo标记一个id，并将此API内容和echo的id以及闭包回调的函数本体存起来。
- 等`WSMessageEvent.php`收到HTTPAPI插件发来的回包时，如果判断到回包中存在echo字段并且是框架已经保存的echo标记，则使用`call_user_func()`函数调用闭包回调函数。
- 在模块中业务代码写起来就是这样：
```php
if($message == "机器人状态"){
    CQAPI::get_status($robot_id, function($response) use ($robot_id){
        CQAPI::send_private_msg($robot_id, [
            "user_id" => 10086, 
            "message" => "机器人QQ目前状态：".($response["data"]["online"] ? "正常" : "离线")
        ]);
    });
}
```

#### Notice、Request事件处理流程
- 接收到的事件上报WSMessageEvent.php
- 将事件上报给每个模块中的`onRequest()`、`onNotice()`静态函数

#### 框架HTTP请求处理流程
- 接收到的事件上报`HTTPEvent.php`
- 在HTTPEvent中写HTTP的业务逻辑。
