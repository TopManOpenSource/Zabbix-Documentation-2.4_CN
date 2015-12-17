
.. _agent:

********************
3 Agent
********************

概览
====

ZabbixAgent被部署在被监视的系统上以监控本地资源和应用（比如硬盘、内存和进程状态等信息。）

agent用来采集操作信息并发送到ZabbixServer做进一步处理，当出现故障时（比如硬盘已满或者某个服务进程崩溃），Zabbix可以向出现故障的那台计算机的管理员发布警报，报告这次故障。

ZabbixAgent由于采用原生的系统调用来采集状态信息，因此效率很高，资源占用很少。

被动检查和主动检查
------------------

ZabbixAgent可以执行被动检查和主动检查。

在被动检查模式下，Agent响应来自ZabbixServer或者Proxy的请求对象，比如CPU load，并且发送结果给ZabbixServer或者Proxy。

主动检查需要更复杂的处理，Agent必须先专门去ZabbixServer请求一次Items的列表，然后定期向Server发送这些Items的新值。

根据items的类别来设置Agent是采用被动检查或者主动检查，ZabbixAgent把items分成两个类别'ZabbixAgent'与'ZabbixAgent(active)'

支持平台
--------
Zabbix agent支持以下平台

 - Linux
 - IBM AIX
 - FreeBSD
 - NetBSD
 - OpenBSD
 - HP-UX
 - Mac OS X
 - Solaris: 9, 10, 11
 - Windows: 2000, Server 2003, XP, Vista, Server 2008, 7

安装
----
参阅*安装介绍*中关于ZabbixAgent的内容

.. note::
        通常，32的Zabbix可以在64系统上运行，但某些情况下可能会报错退出

Unix上的Agent进程（独立服务进程）
---------------------------------
Zabbix Agent以后台服务的方式运行在被监视的系统上

运行以下命令启动ZabbixAgent ::

  shell> zabbix_agentd -h

ZabbixAgent可以使用下面的命令行参数：::

  -c --config <config-file>      配置文件的绝对路径
  -p --print                     打印已知的item然后退出
  -t --test <item key>           测试指定的Item然后退出
  -h --help                      显示帮助信息
  -V --version                   显示版本号
  -R --runtime-control <option>  执行管理功能
   
.. note::
   runtime control不支持OpenBSD、NetBSD和Windows操作系统

runtime control
---------------

runtime control选项：


+-----------------------------------+--------------------------------------------+--------------------------------------------------------+
| 选项                              |                    描述                    | 操作对象                                               |
+-----------------------------------+--------------------------------------------+--------------------------------------------------------+
| log_level_increase[=<**target**>] | 增加日志级别，如无特殊指定，则影响所有进程 | **PID**-进程标识符，（范围从1到65535）                 |
|                                   |                                            |                                                        |
|                                   |                                            | **Process Type** -所有特别指定的进程类别，比如，poller.|
|                                   |                                            |                                                        |
|                                   |                                            | **process type,N** -进程类别与进程号，比如 poller,3    |
|                                   |                                            |                                                        |
+-----------------------------------+--------------------------------------------+                                                        |
| log_level_decrease[=<**target**>] | 降低日志级别，如无特殊指定，则影响所有进程 |                                                        |
+-----------------------------------+--------------------------------------------+--------------------------------------------------------+

对于单个的Zabbix进程来说，允许更改日志级别的PID范围是从1到65535，如果系统上拥有很多PID，就可以使用<process type,N>目标参数用来调整单个进程的日志级别。

使用runtime control 来更改日志级别：::

        Increase log level of all processes:
        shell> zabbix_agentd -c /usr/local/etc/zabbix_agentd.conf -R log_level_increase

        Increase log level of second listener process:
        shell> zabbix_agentd -c /usr/local/etc/zabbix_agentd.conf -R log_level_increase=listener,2

        Increase log level of process with PID 1234:
        hell> zabbix_agentd -c /usr/local/etc/zabbix_agentd.conf -R log_level_increase=1234

        ecrease log level of all active checks processes:
        shell> zabbix_agentd -c /usr/local/etc/zabbix_agentd.conf -R log_level_decrease="active checks"

进程属主
----------
Zabbix agent被设计为非root用户运行，可以从任何非root帐户启动Zabbix。所以你可以以任意一个非root身份启动ZabbixAgent而不会遇到报错。

如果你尝试以root身份运行ZabbixAgent，它将自动切换到'zabbix'身份运行，你的系统上一定会有这个账号。你只有修改配置文件中'AllowRoot'项为肯定后，才能以root身份启动ZabbixAgent

配置文件
--------
参阅*配置文件*一章获取获取Zabbix_agentd的配置文件的详细信息。

在Windows操作系统上运行Agent
-----------------------------

参阅*ZabbixAgent 运行在微软操作系统上*一节以获取如何在Windows上安装、运行、配置Zabbix Agent。

在Windows上的Agent的命令语法：::

 zabbix_agentd.exe [-c config-file]
 zabbix_agentd.exe [-c config-file] -p
 zabbix_agentd.exe [-c config-file] -t item-key
 zabbix_agentd.exe [-c config-file] -i [-m]
 zabbix_agentd.exe [-c config-file] -d [-m]
 zabbix_agentd.exe [-c config-file] -s [-m]
 zabbix_agentd.exe [-c config-file] -x [-m]
 zabbix_agentd.exe -h
 zabbix_agentd.exe -V

以下是常用的参数

选项：::

  -c --config <file>    配置文件的绝对路径，默认在c:\zabbix_agentd.conf
  -p --print            打印已知Item并退出
  -t --test <item key>  测试指定Item并退出
  -h --help             显示帮助信息
  -V --version          显示版本号

功能：::

  -i --install          作为服务安装Zabbix agent
  -d --uninstall        卸载Zabbix agent
  -s --start            启动Zabbix agent服务
  -x --stop             停止 Zabbix agent 服务
  -m --multiple-agents  服务名将包含主机名

配置文件
--------
参阅*配置文件*选项获取Zabbix Windows 客户端的更多选项

返回码
------

Zabbixagent2.2之前的版本成功启动后返回代码0，出现故障时返回代码255，此后的版本在成功启动时返回代码0，失败时返回代码1。
