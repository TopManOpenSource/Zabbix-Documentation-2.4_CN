
.. _javagateway:

***************
5 Java gateway
***************

概览
====
Zabbix 2.0 通过引入一个新的名为 "Zabbix Java gateway"的守护进程，增加了对JMX应用进行监视的原生支持。Zabbix Java gateway 是一个用Java写的后台守护进程，当Zabbix Server想要知道运行在host上的JMX应用的某个特定参数的值的时候，就会使用到Zabbix Java gateway,Zabbix Java gateway使用 `JMX management API <http://www.oracle.com/technetwork/java/javase/tech/javamanagement-140525.html>`_ 来请求远程应用的值。被监视的应用不需要额外安装任何附加软件，只需要从 ``-Dcom.sun.management.jmxremote`` 命令参数启动。

Java gateway 只能以被动代理的方式接受由ZabbixServer或者ZabbixProxy发起的传入链接，与Zabbix Proxy不同的是，它可能会被ZabbixProxy调用（Proxy不能链接JMX应用），访问每个javagateway的 行为是直接被写在ZabbixServer或者ZabbixProxy的配置文件里面的，因此一个 Java gateway 可能会被其他任何ZabbixServer或者Proxy访问。如果一个host同时拥有 **JMX agent** 的items和其他类别的items，那么只有 **JMX agent** 的计数器的值会通过Java gateway传输。

当有一个item的值通过Java gateway被更新，Zabbix Server或者Proxy将会连接这个Java gateway 并请求这个item的值，而这个Java gateway就会检索并传回这个值到Server或Proxy。由此可知，Java gateway不在本地缓存任何数据。

Zabbix Server或者Proxy使用特定的进程链接Java gataway,使用选项 **StartJavaPollers** 控制。在Java gateway启动多个线程，由 **START_POLLERS** 控制。在Server端，在服务器端，如果需要在超时秒数以上的连接，它将被终止，但Java gateway能还在忙着从JMX计数器中检索值。为了解决这个问题，Zabbix 2.0.15、2.2.10和2.4.5在Java gateway引入了 **time out** 参数以允许对JMX的网络操作设置超时时间。

为了获得更好的性能，Server或者Proxy会将很多请求汇集在一起，形成一个请求池，并尝试通过javagateway在一个连接中尽可能地对单个JMX应用一起发送这些请求。

建议 **StartJavaPollers** 小于或者等于 **START_POLLERS** ，不然在某些情况下Java gateway没有可用的进程为传入链接服务。

以下章节描述了如何获取并运行Zabbix Java gateway,如何配置ZabbixServer（或者Zabbix proxy）来使用Java proxy监视JMX应用。还有如何在Zabbix GUI中配置Zabbix items使他与JMX计数器保持一致


5.1 获取Zabbix Java gateway
-----------------------------

这里有两个办法来获取Java gateway,一种是从Zabbix网站上下载软件包，另一种是从源代码自行编译。


5.11 从Zabbix网站上下载
^^^^^^^^^^^^^^^^^^^^^^^
Java gateway软件包（RHEL, Debian, Ubuntu）可以从此链接下载：http://www.zabbix.com/download.php

5.12 从源代码编译
^^^^^^^^^^^^^^^^^
为了从源代码编译 Java gateway，你必须首先运行 ``./configure`` 脚本并加上 ``--enable-java`` 参数，给脚本加上 ``--prefix`` 参数指定一个不同于默认的/usr/local安装路径是一个明智的选择。因为安装Java gateway将会创建一个完整的目录树而不仅仅运行一个单独的可执行文件。

::

$ ./configure --enable-java --prefix=$PREFIX

为了编译并打包Java gateway到一个JAR文件，可以使用 ``make`` 命令，前提是你的环境变量中包含 ``javac`` 和 ``jar`` 可执行文件。

::

$make

现在你的src/zabbix_java/bin目录下有了一个zabbix-java-gateway-$VERSION.jar文件。如果你愿意从src/zabbix_java运行Java gateway，请继续阅读如何配置和运行Java gateway，否则，请确定你有足够的权限运行 ``make install`` 

::

$make install


5.2  Java gateway 文件分发概览
-------------------------------

不管你用什么手段得到了Java gateway 安装包，最后你都会得到一堆shell脚本、jar文件和位于$PREFIX/sbin/zabbix_java下的配置文件。这些文件的作用如下： 

::

 bin/zabbix-java-gateway-$VERSION.jar

Java gateway自身的jar文件。

::

        lib/logback-core-0.9.27.jar
        lib/logback-classic-0.9.27.jar
        lib/slf4j-api-1.6.1.jar
        lib/android-json-4.3_r3.1.jar

Java gateway的依赖文件: `Logback <http://logback.qos.ch/>`_ , `SLF4J <http://www.slf4j.org/>`_, 与 `Android JSON <https://android.googlesource.com/platform/libcore/+/master/json/>`_ 的库。

::

 lib/logback.xml  
 lib/logback-console.xml

Logback的配置文件

::

        shutdown.sh  
        startup.sh
启动或者关闭Javagateway的快捷脚本。

::

 settings.sh

上面的启动和关机脚本需要获取的配置文件。

5.3 配置和运行Java gateway
--------------------------

默认情况下 Java gateway 监听10052端口，如果你打算让它监听其他端口，请参阅 :ref:`Zabbix Java gateway 配置文件`

.. warning::

   10052端口不在 IANA端口列表:http://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.txt 中

一旦你确定了配置，你可以使用脚本来运行Java gateway:

::

$ ./startup.sh

同样地，如果你不再需要它，运行脚本来关闭：

::

$ ./shutdown.sh

注意这样将断开与Server或者Proxy的链接。 Java gateway 是轻量级应用，不需要数据库。


5.4 为使用Java gateway配置Zabbix Serve 
----------------------------------------

现在 Java gateway已经在运行了，你必须告诉ZabbixServer如何找到Java gateway,详细步骤已经在 :ref:`服务配置文件` 说的很详细了。如果运行JMX应用的host是被Proxy监控的，请参阅 :ref:`Proxy配置文件` 

::

 JavaGateway=192.168.3.14
 JavaGatewayPort=10052

默认情况下，server不会开启任何新的进程用于监视JMX。如果你要使用它，就必须指定轮询进程数，以相同的方式指定轮询数和trappers 

::

 StartJavaPollers=5

配置完成后别忘了重新启动一下Server或者proxy。

5.5 调试Java gateway

如果Java gateway有任何错误或者或者在前端看到一个item的错误信息描述的不够详细，你可能希望 去查看日志文件

默认情况下，日志文件以“info”级别 激活并写入到在 /tmp/zabbix_java.log文件中，有时这个级别产生的日志不够详细，需要调整到“debug”级别。修改 lib/logback.xml，定位到 <root> 标签，修改为 "debug"

 ::

 <root level="debug">
 <appender-ref ref="FILE" />
 </root>

注意这样操作将断开与Server 或Proxy的链接。但你修改logback.xml后无需重启Zabbix Java gateway，修改内容会自动生效。当你结束debug以后，可以将log级别改回到“info”.

如果你想改变log文件的存储形态,比如保存在数据库里，请调整logback.xml来实现。详见 :ref:`Logback 手册` 。

把Java gateway启动为一个控制台应用程序而不是一个后台守护进程，可以更好地调试程序。在settings.sh中注释掉 PID_FILE 选项，就可以启动Java gateway为一个控制台守护进程，同时不使用lib/logback.xml。但是这样仅仅把日志打印到控制台，为了能记录日志，还是调整配置文件中的日志级别到"debug" 为好。

最后，由于Java gateway使用 SLF4J 进行日志生成和保存，您可以通过在lib目录中放置一个适当的JAR文件来替换手册中文版与你选择的框架，详见 `SLF4J 手册 <http://www.slf4j.org/manual.html>`_ 获取更多信息。





