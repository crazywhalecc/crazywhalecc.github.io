# 快速开始

使用此框架开发你需要掌握PHP语言、PHP语言的回调函数特性以及PHP面向对象的编程知识。

本框架是将功能以模块化进行管理，例如你写了一套签到的功能，包含签到、查看排行、存储数据等指令，那么应该将这一类放到一个模块里。

在本框架中除特殊情况外，你只需要编写```/src/cqbot/mods/```下面存放的模块代码即可，其他均为框架完成。

在每一次代码修改后，只需回复机器人```reload```即可重启框架进行重载代码，无需登陆服务器进行操作。

相关事件上报的数据结构请查阅[HTTPAPI插件文档](https://cqhttp.cc/docs/#/Post)。

## 模块结构

本框架默认提供了一个```Admin.php（管理）```模块，一个```Example.php（示例）```模块，在开始前，你可以直接将代码写入Example.php或Admin.php中。

假设我们新创建一个模块是```YourMod.php```，代码如下：
```php {highlight:[9]}
<?php
class YourMod extends ModBase{
    public function __construct(CQBot $main, $data){
        parent::__construct($main, $data);
        $message = $data["message"];
        $user_id = $data["user_id"];
        //这里$data变量和$this->data变量为HTTPAPI事件上报中消息类(post_type=message)的json
        //这里写你的通过消息内容处理的代码
        $this->reply("这里是快速回复用户消息的函数");
    }
}
```

以上就可以快速地进行一个简单的快速消息回复，相关逻辑只需自己编写即可。

## 使用内建分割函数处理消息事件

基本框架结构为上方。但你可能看到了，在示例模块中，使用了一个```execute($it)```函数。这个函数是框架内建的一个字符串分割解析函数。

比如你的一条消息是```随机数 5 10```，在execute函数中传入的```$it```变量内容就是数组```['随机数','5','10']```。

使用execute函数也很简单，不需要你去在构造函数调用execute，只需在构造函数中加入```$this->split_execute=true;```即可。

```php {highlight:['4-6']}
<?php
function execute($it){
    switch($it[0]){
        case "随机数":
            $this->reply("你的随机数是".mt_rand($it[1], $it[2]));
            return true;
    }
    return false;
}
```
## 处理Notice事件

Notice事件处理只需加一个名字为```onNotice($req)```的静态方法即可。```$req```的数据结构就是HTTPAPI插件上报的Notice事件数据结构
```php
<?php
static function onNotice($req){
    $notice_type = $req["notice_type"];
    //这里编写你的Notice事件处理代码
}
```

## 处理Request事件

Request事件处理只需加一个名字为```onRequest($req)```的静态方法即可。```$req```的数据结构就是HTTPAPI插件上报的Request事件数据结构
```php
<?php
static function onRequest($req){
    $request_type = $req["request_type"];
    //这里编写你的Request事件处理代码，例如好友添加请求
}
```