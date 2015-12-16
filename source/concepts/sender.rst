.. _sender:

************
6 sender
************

概览
====
Zabbix sender 是一个可能用来向ZabbixServer进程发送性能数据的命令行工具。
这个工具通常被用来在长时间运行的用户脚本中定期发送可用性和性能数据。

运行ZabbixSender
================

一个在unix下运行Zabbixsender的例子 ::

        shell> cd bin
        shell> ./zabbix_sender -z zabbix -s "Linux DB3" -k db.connections -o 43

在这里:

 * z - ZabbixServerhost，（推荐使用IP地址）
 * s - 被监视的主机（跟在ZabbixServer前端中注册的相同）
 * k - item key
 * o - 发送的数值

.. waring::
   如果你发送的对象中中含有空白，那么一定要用双引号括起来。

参阅 :ref:`Zabbix sender manpage<zabbix_sender>` 获取更多信息

Zabbix sender 接受UTF-8编码的字符串（Unix、类unix和windows上均是如此。）

同样地，在Windows上使用如下命令: ::
 
        zabbix_sender.exe [options]
 
                                                                                
自Zabbix 1.8.4版本以来，Zabbix_sender 实时发送方案改进为持续收集多个连续传递给它的值并通过一个单独的链接把他们发送到Server。
两个值产生的时间间隔小于0.2s即可放在同一个堆栈中发送，不过两个值的最大间隔依然保持在1S。
如果从一个输入文件中发送过多数据，Zabbixsender 将分批次发送这个值，每批次发送250个直到所有数据都发送完毕。比如：::

        # zabbix_sender -z 127.0.0.1 -i /tmp/traptest.txt 
        Info from server: "Processed 250 Failed 0 Total 250 Seconds spent 0.002668"
        Info from server: "Processed 50 Failed 0 Total 50 Seconds spent 0.000540"
        sent: 300; skipped: 0; total: 300

文件中的所有条目自上而下地被发送。
如果目标item被triggers引用，则输入文件里的所有的时间戳都必须按照升序排列，否则计算出来的结果就不精确

.. note::
   如果指定的配置文件中出现无效的输入参数，ZabbixSender将会中止运行（不采用参数=值这种方法）
