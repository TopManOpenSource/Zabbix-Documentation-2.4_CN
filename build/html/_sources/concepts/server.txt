
.. _server:

************
2 Server
************


概览
======
ZabbixServer是整个Zabbix里面最重要的部分。
Server 执行轮询与获取数据，计算Tiggers并向用户发送通知，接收ZabbixAgent与ZabbixProxy发送的系统健康性和完整性数据，Server本身也可以使用简单的检查服务来检查一些网络服务，比如WebServer和Mail服务。

Server是所有配置信息、统计信息和业务数据的存储中心，当任何一个被监视的系统出现故障时zabbixServer都将触发警报提醒管理员，这一部分相对于存储来说是独立的。

ZabbixServer的基本功能可以清晰地划分成三个部分，它们分别是：Zabbix Server，Web前端，和数据库存储器。

所有的ZabbixServer配置信息保存在数据库，包括Server和Web前端接口。比如说，当你使用Web前段或者API创建了一个新的item，这个item将会被添加到数据库中的item列表中，然后，Zabbix会每一分钟从items列表中查询处于活动状态的items，并且放置到ZabbixServer的缓存中。这就是为什么任何从Web前段做出的更改最多需要两分钟时间才能在最新的数据状态中被显示出来。

Zabbix进程
==========

Zabbix Srver作为一个守护进程被运行，使用如下指令来启动ZabbixServer:
::

   Shell> cd sbin
   Shell>./zabbix_server

你可以在在ZabbixServer命令中使用如下参数：
::

   -c --config(文件名)         配置文件的绝对路径（默认是在/etc/zabbix/zabbix_server.conf）
   -R --runtime-control (可选) 执行管理功能
   -h --help                   显示此帮助信息
   -V --version                显示版本号

.. note::
        Runtime control 功能不支持OpenBSD和NetBSD系统

命令参数举例：
::

   shell> zabbix_server -c /usr/local/etc/zabbix_server.conf
   shell> zabbix_server --help
   shell> zabbix_server -V

Runtime control
===============

Runtime control 选项：
        
+-----------------------------------+--------------------------------------------+--------------------------------------------------------+
| 选项                              |                    描述                    | 操作对象                                               |
+===================================+============================================+========================================================+
| config_cache_reload               | 重新载入配置缓存，如果缓存正在加载，则忽略 |                                                        |
+-----------------------------------+--------------------------------------------+--------------------------------------------------------+
| log_level_increase[=<**target**>] | 增加日志级别，如无特殊指定，则影响所有进程 | **PID**-进程标识符，（范围从1到65535）                 |
|                                   |                                            | **Process Type** -所有特别指定的进程类别，比如，poller.|
|                                   |                                            | **process type,N** -进程类别与进程号，比如 poller,3    | 
|                                   |                                            |                                                        |
+-----------------------------------+--------------------------------------------+                                                        | 
| log_level_decrease[=<**target**>] |  降低日志级别，如无特殊指定,则影响所有进程 |                                                        |  
|                                   |                                            |                                                        |  
|                                   |                                            |                                                        |
+-----------------------------------+--------------------------------------------+                                                        | 
| log_level_decrease[=<**target**>] | 降低日志级别，如无特殊指定，则影响所有进程 |                                                        |  
+-----------------------------------+--------------------------------------------+--------------------------------------------------------+

对于单个的Zabbix进程来说，允许更改日志级别的PID范围是从1到65535，如果系统上拥有很多PID，就可以使用<process type,N>目标参数用来调整单个进程的日志级别。


举例说明使用runtime control 来重新载入配置缓存：
::
        shell> zabbix_server -c /usr/local/etc/zabbix_server.conf -R config_cache_reload

举例说明使用 runtime control来改变日志级别：
::
        Increase log level of all processes:
        shell> zabbix_server -c /usr/local/etc/zabbix_server.conf -R log_level_increase

        Increase log level of second poller process:
        shell> zabbix_server -c /usr/local/etc/zabbix_server.conf -R log_level_increase=poller,2

        Increase log level of process with PID 1234:
        shell> zabbix_server -c /usr/local/etc/zabbix_server.conf -R log_level_increase=1234

        Decrease log level of all http poller processes:
        shell> zabbix_server -c /usr/local/etc/zabbix_server.conf -R log_level_decrease="http poller"

进程属主
========

ZabbixServe被设计为不被root用户运行。任何一个非root用户都可以启动ZabbixServe，所以你可以以任意一个非root用户启动ZabbixServer而不会报错。


如果你想尝试一下使用"root"用户运行一下ZabbixServer，该进程将被切换到内置的"zabbix"用户运行,此用户在你的系统上一定会存在,仅当你把ZabbixServer配置文件中的'AllowRoot'项修改为允许时，才能使用'root'身份运行ZabbixServer进程。



配置文件
========

参阅*配置文件章节*获得关于ZabbixServer配置文件的更多信息

启动脚本
========

这个脚本用来随操作系统的启动/关闭而自动启动/停止ZabbixServer的进程，位于 misc/init.d

支持的平台
==========
考虑到安全性和关键任务的操作特性，UNIX是唯一可以持续提供必要的性能、容错性和恢复能力的操作系统，并且在运行Zabbix的表现上领先市场。
Zabbix在以下平台上测试通过
 - Linux

 - Solaris

 - AIX

 - HP-UX

 - Mac OS X

 - FreeBSD

 - OpenBSD

 - NetBSD

 - SCO Open Server

 - Tru64/OSF1


.. note::
   在其他的类Unix系统上Zabbix也可以工作的很好
