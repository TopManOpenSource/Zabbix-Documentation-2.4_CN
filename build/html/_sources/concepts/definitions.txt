
.. _Zabbix 术语:

***************
1 Zabbix 术语
***************

概览
====
本章解释了Zabbix系统中常用术语的含义

术语
====

host
----

*-被监控的网络设备，用IP地址或者DNS域名标识*

host group
-----------

*-由host组成的逻辑意义上的组，可能包含了hosts和templates(模板)，但是一个host group内的hosts和templates没有任何关系，只有在为不同的用户分配对hosts的访问权限时才使用这个概念*

item
----

*-Zabbix从host接收的一段特定的数据*

trigger
-------

*-一个逻辑表达式，它定义了一个故障阈值，并以此"评估"从item中收到的数据*

当收到的数据内容表明特定的某个值超过了规定的阈值，triggers将状态从“OK”切换到“故障”，当收到的数据内容表明特定的某个值低于阈值，则triggers将状态置为“OK”。

event
-----
*-一个值得关注的独立事件，比如某个trigger的状态改变或者自动发现/客户端被自动配置*

action
------

*-对一个event发生时的预定义的应对措施*

一个action包含一个操作（比如发送一个通知）和一个环境（比如操作进行的时候的操作环境）

escalation
----------

*内置于action中的操作方案，发送到远程服务器并执行的命令序列*

media
-----
*-提供通知的一种手段，信息传递通道*

notification
------------

*-一段通过选定的信息通道发送给用户的信息*

remote command
--------------

*-当远程host满足某些条件时自动执行的、预先编写好的代码*

temple
-------
*-一个设定各种参数（items, triggers, graphs, screens, applications, low-level discovery rules, web scenarios）的集合，准备应用在一台或几台hosts上*

application
-----------

*-一组items*

web scenario
------------

*-一个或几个用来检查网站可用性的HTTP请求*

frontend
---------

*-Zabbix提供的网页界面*

Zabbix API
-----------

*-Zabbix API允许你使用JSON RPC协议来创建、升级和接收Zabbix的某些项目（比如hosts、items、图形和其他数据）或执行其他常见任务*

Zabbix server
-------------

*-Zabbix软件的处理核心，负责与代理、客户端的交互，计算tiggers,发送报告，存储数据*

Zabbix agent
-------------

*-部署在监控目标上的应用，主动监控所在hosts的环境和应用*

Zabbix proxy
------------
*-一个代替ZabbixServer收发数据的进程，可以分担ZabbixServer的计算压力*

.. note::
   专有名词在不同人的文档中都有不同的翻译，为了保持语义一致，此处对专有名词没有做翻译而是直接保留了原文。所以读起来有点中英混杂。
 


