
.. _proxy:

***************
4 Proxy
***************

概览
====

Zabbix Proxy基本上是代替ZabbixServer从一台或者多台被监视的设备上收集数据并且发送给ZabbixServer，所有搜集到的数据都缓存在本地然后发送给Server做进一步的处理。


ZabbixProxy的部署是一个可选项，但是，一台ZabbixProxy可以极大地缓解单台ZabbixServer的负担。如果完全由Proxy收集数据，那么Server进程就会减少对于CPU和硬盘I/O的需求。

对于需要集中监控但没有本地管理员的偏远地区、分支机构或者网络，部署一台ZabbixProxy是一个理想的解决方案。

ZabbixProxy需要一个独立的数据库

.. note::
   支持Zabbix Proxy的数据库有 SQLite, MySQL 和 PostgreSQL，使用Oracl与IBM DB2需要自担风险,并且，在使用Low-level discovery的时候可能会出现问题。

参阅 *分部署环境中使用ZabbixProxy*

Proxy进程
=========

Zabbix Proxy作为一个后台守护进程运行，使用如下命令运行：
::

    shell> cd sbin
    shell> ./zabbix_proxy

你可以对ZabbixProxy使用如下参数:
::
        -c --config <file>               配置文件的绝对路径

        -R --runtime-control <选项>      执行管理员功能
        
        -h --help                        显示此帮助信息
        
        -V --version                     显示版本号

.. note::
   Runtime control 不支持OpenBSD与NetBSD


命令参数举例：
::

   shell> zabbix_proxy -c /usr/local/etc/zabbix_server.conf
   shell> zabbix_proxy --help
   shell> zabbix_proxy -V

Runtime control
-----------------
Runtime control 选项：

+-----------------------------------+--------------------------------------------+--------------------------------------------------------+
| 选项                              |                    描述                    | 操作对象                                               |
+===================================+============================================+========================================================+
| config_cache_reload               | 重新载入配置缓存，如果缓存正在加载，则忽略 |                                                        |
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
        shell> zabbix_proxy -c /usr/local/etc/zabbix_server.conf -R config_cache_reload

举例说明使用 runtime control来改变日志级别：
::
        Increase log level of all processes:
        shell> zabbix_proxy -c /usr/local/etc/zabbix_server.conf -R log_level_increase

        Increase log level of second poller process:
        shell> zabbix_proxy -c /usr/local/etc/zabbix_server.conf -R log_level_increase=poller,2

        Increase log level of process with PID 1234:
        shell> zabbix_proxy -c /usr/local/etc/zabbix_server.conf -R log_level_increase=1234

        Decrease log level of all http poller processes:
        shell> zabbix_proxy -c /usr/local/etc/zabbix_server.conf -R log_level_decrease="http poller"

进程属主
=========
Zabbixproxy被设计为不被root用户运行。任何一个非root用户都可以启动ZabbixProxy，所以你可以以任意一个非root用户启动ZabbixProxy而不会报错。


如果你想尝试一下使用"root"用户运行一下ZabbixProxy，该进程将被切换到内置的"zabbix"用户运行,此用户在你的系统上一定会存在,仅当你把ZabbixProxy配置文件中的'AllowRoot'项修改为允许时，才能使用'root'身份运行ZabbixProxy进程。


配置文件
========

参阅:ref:`配置文件 <配置文件>` 一节获取关于Zabbix Proxy配置文件的详细信息。
