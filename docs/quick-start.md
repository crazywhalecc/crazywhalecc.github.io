# 快速开始

使用此框架开发你需要掌握PHP语言、PHP语言的回调函数特性以及PHP面向对象的编程知识。

本框架是将功能以模块化进行管理，例如你写了一套签到的功能，包含签到、查看排行、存储数据等指令，那么应该将这一类放到一个模块里。

在本框架中除特殊情况外，你只需要编写```/src/cqbot/mods/```下面存放的模块代码即可，其他均为框架完成。

在每一次代码修改后，只需回复机器人```reload```即可重启框架进行重载代码，无需登陆服务器进行操作。

相关事件上报的数据结构请查阅[HTTPAPI插件文档](https://cqhttp.cc/docs/#/Post)。

本框架默认提供了一个```Admin.php（管理）```模块，一个```Example.php（示例）```模块，在开始前，你可以直接将代码写入Example.php或Admin.php中。

假设我们新创建一个模块是```YourMod.php```，代码如下：
```php {highlight:['5-9']}
<?php
class YourMod extends ModBase{
    public function __construct(CQBot $main, $data){
        parent::__construct($main, $data);
        $message = $data["message"];
        $user_id = $data["user_id"];
        if($message == "hello") {
            $this->reply("Hello，".$user_id);
        }
        
        //这里$data变量和$this->data变量为HTTPAPI事件上报中消息类(post_type=message)的json
        //这里写你的通过消息内容处理的代码
    }
}
```

以上就可以快速地进行一个简单的快速消息回复，相关逻辑只需自己编写即可。

> 快速开始仅做开发引导，和框架的状态等使用无关。
