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