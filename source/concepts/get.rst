
.. _get:

*******
7 get
*******

概览
====
Zabbix get 是一个连接Zabbixagent并取回必需数据的一个应用

这个工具经常用来对Zabbixagent排障

运行Zabbix get
===============

在unix平台上运行Zabbix get来获取另外一个运行在Unix下的Zabbix agent加载的值的列子：::

        shell> cd bin
        shell> ./zabbix_get -s 127.0.0.1 -p 10050 -k "system.cpu.load[all,avg1]"

Zabbix get 接受下列命令行参数 ::
          -s --host <host name or IP>      特定的机器名或者IP地址
          -p --port <port number>          指定机器上Zabbix agent监听的端口，默认是10050
          -I --source-address <IP address> 指定的源IP地址
          -k --key <item key>              指定的需要接收的 item 的Key
          -h --help                        显示此帮助信息.
          -V --version                     显示版本号.

Zabbix get 在Windows下使用同样的命令运行: ::
        zabbix_get.exe [options]

