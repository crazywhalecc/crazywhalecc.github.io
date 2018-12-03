# 配置文件

框架使用```cqbot.json```文件来配置一些基础参数。

> 致敬HTTP-API插件作者，我这里采用了一致的配置文件的文档风格。

| 配置名称 | 默认值 | 说明 |
| -------- | ---- | --- |
| `robot_name` | `CQBot` | 机器人名字 |
| `cqbot_version` | `1.0.0` | 机器人版本 |
| `config_dir` | `cq_data/config/` | config目录位置 |
| `crash_dir` | `cq_data/crash/` | 框架、PHP报错日志文件夹 |
| `user_dir` | `cq_data/users/` | 用户实例储存的文件夹 |
| `cq_data` | `cq_data/` | 框架数据根目录 |
| `admin_group` | 空 | 管理QQ群号 |
| `access_token` | 空 | 连接框架的token |
| `info_level` | `0` | 终端显示log等级 |
| `admin` | 空数组 | 管理员QQ号列表 |
| `save_user_data` | `true` | 开启/关闭储存用户实例对象 |
| `swoole_host` | 空 | 框架监听地址 |
| `swoole_port` | 空 | 框架监听端口 |
| `swoole_worker_num` | `1` | 框架工作进程数量 |
| `swoole_dispatch_mode` | `2` | Swoole工作模式 |
| `swoole_log_file` | `cq_data/crash/swoole.log` | Swoole扩展的错误日志 |
| `swoole_use_tick` | `true` | 开启/关闭内建计时器 |
| `swoole_tick_interval` | `1000` | 内建计时器激活间隔(ms) |