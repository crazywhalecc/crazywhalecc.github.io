# 模块

CQBot-swoole 框架采用了模块系统，前面的[快速开始](/docs/quick-start)已经简单写了模块的用法，本章将对模块的细节进行介绍。

> 以下所有提到的内容均写到```你的模块.php```的class里面

## 模块结构

模块需要基于```ModBase```类进行构建，构建函数需要对父类构建函数传递参数。
```php
<?php
class YourMod extends ModBase{
    public function __construct(CQBot $main, $data){
        parent::__construct($main, $data);
    }
}
```

其中传入的```$main```变量为```CQBot```对象，是一个调用各个模块的类，当接收到```message```类型的事件上报后进行```声明(new)```。

```$data```变量为HTTP-API插件上报的整个事件（已经json反序列化的数组），可以根据自己的需要进行处理。

## 内建分割字符串函数
在示例模块中，使用了一个```execute($it)```函数。这个函数是框架内建的一个字符串分割解析函数。

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

## 激活计时器

如果在```cqbot.json```中设置开启了```swoole_use_tick```，你就可以在自己的模块中添加```onTick($tick)```静态方法，根据```swoole_tick_interval```设置的间隔时间(ms)定时激活每个模块的onTick函数。

下面的例子我们假设我们使用默认的tick interval（间隔为1s）。每15分钟保存一次Cache中的缓存数据。

```php
<?php
static function onTick($tick){
    if($tick % 900 == 0){
        CQUtil::saveAllFiles();
    }
}
```

## 初始化内存数据

在模块中写了初始化函数```initValues()```后，每次**重启**或**启动**框架后都会执行。此函数内放置从文件、数据库要读取到内存中的缓存。
```php
static function initValues(){
    Cache::set("hello_msg", file_get_contents(CONFIG_DIR."hello.txt"));
}
```

## 保存内存数据

在模块中写了初始化函数```saveValues()```并设置了上面提到的计时器后，就可以实现隔一段时间执行saveValues方法进行储存。

> 单个模块中尽量去保存这个模块需要储存的文件，不要去串模块进行保存。
> 因为模块的意义就是为了区分功能，使各类功能在模块中更独立。
```php
static function saveValues(){
    file_put_contents(CONFIG_DIR."hello.txt", Cache::get("hello_msg"));
}
```

