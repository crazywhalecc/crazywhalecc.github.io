# API及响应处理

框架封装了[HTTP-API插件](https://cqhttp.cc)的API，在模块内可任意调用。因为HTTP-API插件的文档已经足够详细，本框架也使用了魔术方法来映射对应的API进行操作。

API封装类为```CQAPI```，使用静态方式调用此类中的方法即可。

魔术方法使用也很简单，```CQAPI::对应API($self_id, $params, $callback);```

> 由于框架本身适用于多机器人同时连接，故所有API都是支持多账号的。

```php
// 不使用回调函数
CQAPI::send_private_msg(10086, ["user_id" => 12345, "message" => "Hello"]);
```

> 框架整体采用异步的方式来处理回调和其他响应处理，故无需担心调用函数会阻塞。

上面的例子中，send_private_msg为对应[HTTP-API插件](https://cqhttp.cc)的API接口，其他接口只需替换即可。

所有API接口都使用三个固定参数，第一个```$self_id```为发送的机器人的QQ号，可以用WebSocket连接对象或事件上报的```self_id```获取机器人QQ。

第二个```$params```为数组，内容是HTTPAPI插件对应API的参数，例如上方例子中的user_id和message为发送私聊消息接口所需要的参数。

第三个为回调，下面的下面是回调函数的说明。

## 框架API适用性

当你使用PhpStorm等IDE打开框架进行编写时，一般情况下IDE无法识别魔术方法下的函数等，但本框架提供了一些识别和兼容。

此外，你在访问CQAPI类时，也可访问**驼峰命名法**的API，例如```CQAPI::send_group_msg```对应```CQAPI::sendGroupMsg```，也是可以使用的。

当你尝试去调用一个未知的API时，框架会输出error级别的log，同时访问不存在的方法时候也会返回false，可以使用返回值判断函数是否存在。

## 框架API响应处理

由于框架主体采用websocket与HTTPAPI插件连接，所以一切通信都是全双工下进行且**异步**的。

如果需要在消息[响应状态码](https://cqhttp.cc/docs/4.6/#/API?id=%E5%93%8D%E5%BA%94%E8%AF%B4%E6%98%8E)后执行自己的代码，你可以使用框架提供的便捷异步回调函数。

```php
CQAPI::send_group_msg(12345, ["group_id" => 100000, "message" => "test"], function ($response, $origin){
    if($response["retcode"] == 200){
        echo "成功地发出了消息: ".$origin["params"]["message"];
    }
});
```

其中```$response```变量是响应内容，```$origin```是通过API接口发出去的内容。

上面的示例为[闭包写法](https://www.cnblogs.com/melonblog/archive/2013/05/01/3052611.html)，闭包可以让代码逻辑更清晰，避免出现乱调用的情况。当然，你也可以坚持使用指定对象的成员方法或静态方法。

```php
//调用静态函数
CQAPI::send_group_msg(12345, ["group_id" => 100000, "message" => "test"], "YourClass::yourMethodName");

//调用动态函数
CQAPI::send_group_msg(12345, ["group_id" => 100000, "message" => "test"], [$your_object, "yourMethodName"]);
```

## 模块中reply使用回调函数

TODO