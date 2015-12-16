

.. _Zabbix概览:

****************
4.Zabbix概览
****************

体系结构
========
Zabbix由几个主要的软件组成，各个部分的功能概述如下

Server
--------
Zabbix Server是整个体系结构里最重要的组件。Agent向ZabbixServer报告hosts的可用性、完整性和统计数据。Server也是一个重要的仓库，保存着所有的配置信息、统计信息和操作数据。

Web接口
-------
为了能使用户从任意地点和任意平台上访问zabbix,Zabbix提供了一个Web前端。Web前端是ZabbixServer的一部分，经常（但不是必须）跟ZabbixServer运行在同一台物理机上

.. note::

   请注意，如果你使用了SQLlite作为数据库，那么Web前端必须跟ZabbixServer运行在一台机器上。

代理
-----
Zabbix 代理可以代为ZabbixServer收集性能和可用性设计。代理组件在Zabbix体系结构中并非必须，但代理本身可以很好地分担ZabbixServer的负载

客户端
------
Zabbix客户端部署在被监控的机器上，主动收集本地资源状况和本地运行的应用运行状况，并且把这些信息汇总后汇报给ZabbixServe。

数据流
-------
此外，整体上观察一下Zabbix中的数据流是非常重要的。为了创建一个item来搜集数据，需要先创建一个host，也就是说你首先要有一个item，才能创建一个trigger，有了trigger，你才能创建一个action。
因此，如果你打算在你的ServerX的CPU占用过高时收到一个告警，你必须先创建一个ServerX的host,然后在此host条目下创建一个监视CPU的item，当CPU负载过高的时候一个trigger就会被触发，随之就会产生一
个发送email给你的action。
虽然看起来步骤很多很复杂，不过使用模板来创建这些步骤其实很简单，另外这种设计可以让我们创建非常灵活的流程来应对各种复杂的状况。
