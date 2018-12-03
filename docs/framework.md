# 框架

框架中有许多内建的函数可供使用，这里将一一列出。

## CQUtil工具集

我不知道叫工具集是否合适，也没有一个严格的定义，因为这个```CQUtil类```里面存放着一些我不知道如何去分类的函数，干脆就将它们放到这一个类里了。

CQUtil虽然是框架的一部分，但也是可以在reload时候可以重载的。你可以在CQUtil中添加自己想要的全局静态函数，但**谨慎**修改现有函数。

### CQUtil::loadAllFiles

此函数用处是框架worker进程启动后执行的缓存初始化函数，此函数会初始化框架所需要的一些Cache，也同时会执行各个模块内的```initValues()```函数。

### CQUtil::saveAllFiles

此函数用作储存Cache类内存放的缓存数据，同时也会调用各个模块的```saveValues()```函数。

### CQUtil::errorLog

记录错误日志并可选择上报管理QQ群。

```php
//$log为内容，$head是log的头，$send_debug_msg为是否将错误日志上报到管理QQ群
CQUtil::errorLog($log, $head = "ERROR", $send_debug_msg = true);
```

### CQUtil::findRobot

<s>随机</s>返回一个已连接框架的机器人的QQ号

### CQUtil::getAllUsers

返回所有用户实例(User对象)列表。

### CQUtil::getUser

返回用户实例，如果用户不存在则创建。

> 用户实例为框架提供的一个对用户数据进行管理的对象。

```php
$user = CQUtil::getUser(10086);
$user->setNickname("中国移动");
```

### CQUtil::reload

重启框架

### CQUtil::stop

停止框架

### CQUtil::isRobot

判断QQ是否为已经连接到同一框架的另一个机器人或被屏蔽的QQ号（如QQ空间官方号、QQ小冰等）

```php
if(CQUtil::isRobot('2854196306')) return false;
```

## DataProvider文件管理

DataProvider其实严格意义来说我还没写完，目前只是简单地提供了json文件地同步、异步读写。


### DataProvider::getJsonData

阻塞方式读取config目录下地json文件。如果文件不存在或读取错误将返回空数组```array()```。
```php
$obj = DataProvider::getJsonData("a.json");
//$obj为json反序列化后的PHP数组
```

### DataProvider::setJsonData

阻塞方式保存PHP数组为json文件。
```php
DataProvider::setJsonData("a.json", []);
```

### DataProvider::setJsonDataAsync

异步非阻塞方式保存PHP数组为json文件。
```php
DataProvider::setJsonData("a.json", [], function($filename){
    echo "OK!\n";
});
```

### DataProvider::getDataFolder

返回config目录的绝对路径。

### DataProvider::getUserFolder

返回储存user对象的文件夹路径。