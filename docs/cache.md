# 框架Cache缓存

因为本框架是cli长时间运行，故你可以将文件、数据库等内容读取到内存中进行使用，并设置定时保存以防丢失。

框架提供了一个```Cache```类，在模块中你可以随意使用Cache类的以下提供的方法进行内存中的数据读写。

缓存中使用的储存结构是**Key-Value**结构，Value可以为PHP内支持的任意类型变量。一般常用作储存数组，本文档下面的示例中也均以数组为例。

Cache类也保存了一些框架内使用的**特殊对象**。

> 此Cache缓存仅限于同步单进程、异步单进程模式使用，多进程模式无法在多个进程之间共享Cache缓存的变量，需要自行根据多进程的方式进行进程间通信。多线程需要使用Swoole提供的锁来进行线程安全的操作。

## 函数列表

### Cache::get
使用get方法可以获取内存中的数据。
```php
$a = Cache::get("your_key");
```

### Cache::set
使用set方法可以设置内存中的数据。
```php
$ar = [
    "a" => "b"
];
Cache::set("your_key", $ar);
```

### Cache::append
如果你存入的key-value缓存是数组，则可以快捷使用此方法将value追加到已有的数组中。
```php
// Cache =====> "a": [1,2,3]
Cache::append("a", 5);
// Cache =====> "a": [1,2,3,5]
```

### Cache::appendKey
如果你存入的key-value缓存是含有键名的数组，则可以使用此方法设置数组内的键值。
```php
// Cache =====> "list": ["name" => "a", "birth" => "1900"]
Cache::appendKey("list", "sex", "man");
// Cache =====> "list": ["name" => "a", "birth" => "1900", "sex" => "man"]
```

### Cache::unset
使用此方法可以移除Cache中的数据。
```php
Cache::unset("list");
```

### Cache::removeKey
如果你存入的key-value缓存是含有键名的数组，则可以使用此方法删除数组内的键值。
```php
// Cache =====> "list": ["name" => "a", "birth" => "1900"]
Cache::removeKey("list", "name");
// Cache =====> "list": ["name" => "a"]
```

### Cache::removeValue
如果你存入的key-value缓存是数组，则可以快捷使用此方法将数组中的值删除并重新索引键名。
```php
// Cache =====> "a": [1,2,3]
Cache::removeValue("a", 2);
// Cache =====> "a": [1,3]
```

## 其他特殊缓存

其他特殊缓存是用于框架内的一些关键变量，请谨慎修改和使用。

### Cache::$worker_id

此变量储存了当前worker进程的id。

### Cache::$data

此变量中就是Cache上面提到储存的数据的变量。

### Cache::$server

保存的是```swoole_websocket_server```对象，可以在模块任意位置调用进行操作。

### Cache::$scheduler

储存计时器管理器对象的变量。

### Cache::$in_count

储存消息接收条数的计数器。

> 对应```swoole_atomic```的swoole原子计数器，可跨进程读写，这是个对象，操作方式详情请看[Swoole的官方文档](https://wiki.swoole.com/wiki/page/p-atomic.html)。

### Cache::$out_count

储存消息发出条数的计数器。

> 对应```swoole_atomic```的swoole原子计数器。

### Cache::$reload_time

储存框架重启次数的计数器。

> 对应```swoole_atomic```的swoole原子计数器。

### Cache::$api_id

框架内部处理每条API请求的id的计数器。

> 对应```swoole_atomic```的swoole原子计数器。