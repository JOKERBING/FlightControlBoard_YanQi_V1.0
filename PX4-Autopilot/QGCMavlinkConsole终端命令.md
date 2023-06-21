# QGC Mavlink Console终端命令

通过QGC的MAVLink Console工具，可以登录到PX4的字符终端，并通过命令进行操作，监控飞控的状态，常用的指令有如下:

- <kbd>ctrl</kbd> + <kbd>C</kbd>：停止实时获取当前命令

- `top`：获取当前进程

- `help`：查看系统根目录命令和内置进程列表，列出终端模式下所能够执行的命令

  > help
  > 会列出常规的命令和一些可用的模块
  
  > help pwd
  > 可以在help命令后面跟专门命令名可以得到该命令的格式，这样会列出pwd命令的格式
  
- `*** start`：启动某个模块（module）

- `*** stop`：关闭某个模块（module）

- `***  status`：查看某个模块（module）运行状态

- `param`：显示/设置参数的设置

  > param show
  > 显示所有的参数

  > param show SYS_AUTOSTART
  > 显示参数SYS_AUTOSTART的数值

  > param set SYS_AUTOSTART 4600
  > 设置SYS_AUTOSTART的值为4600

- `listener`：用于监听orb message内容

  > listener vehicle_status
  > 监听vehicle_status消息

  > listener vehicle_status 5
  > 监听最近的vehicle_status消息5次






参考原文链接：https://blog.csdn.net/mbdong/article/details/126728771