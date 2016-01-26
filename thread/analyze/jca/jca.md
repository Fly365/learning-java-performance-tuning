IBM JCA工具
==========

# 介绍

IBM JCA工具是IBM提供的一个工具，全称是"IBM Thread and Monitor Dump Analyze for Java".

功能描述如下：一个工具容许在java线程中定位线程挂起/死锁/资源竞争和瓶颈。

> A tool that allows identification of hangs, deadlocks, resource contention, and bottlenecks in Java threads.

IBM Thread and Monitor Dump Analyzer for Java可以分析javacore，并诊断监视器锁和线程活动，以便定位线程挂起，死锁和资源竞争，或者监视瓶颈。

> IBM Thread and Monitor Dump Analyzer for Java analyzes javacore and diagnoses monitor locks and thread activities in order to identify the root cause of hangs, deadlocks, and resource contention or monitor bottlenecks

[官方网站](https://www.ibm.com/developerworks/community/groups/service/html/communityview?communityUuid=2245aa39-fa5c-4475-b891-14c205f7333c)

目前最新版本是Version 4.5.7，发布于2015-2-17。

[下载地址](ftp://public.dhe.ibm.com/software/websphere/appserv/support/tools/jca/jca457.jar)

# 启动

JCA工具下载下来只有一个简单的jar文件，执行简单的java命令就可以启动：

	java -jar jca457.jar

或者发现内存不足时设置最大内存：

	java -Xmx500m -jar jca457.jar





