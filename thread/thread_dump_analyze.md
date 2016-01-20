thread dump分析
===========

## 描述

在java进程的运行过程中，一些Java虚拟机(JVM)可能无法如预期的响应，并时常看上去挂死很长时间甚至直到JVM关闭。找到到这些问题的根本原因不是件容易的事情。

By triggering a javacore when a Java process does not respond, it is possible to collect diagnostic information related to the JVM and a Java application captured at a particular point during execution. For example, the information can be about the operating system, the application environment, threads, native stack, locks, and memory. The exact contents are dependent on the platform on which the application is running.
On some platforms, and in some cases, javacore is known as "javadump." The code that creates javacore is part of the JVM. One can control it by using environment variables and run-time switches. By default, a javacore occurs when the JVM terminates unexpectedly. A javacore can also be triggered by sending specific signals to the JVM. Although javacore or javadump is present in Sun Solaris JVMs, much of the content of the javacore is added by IBM and, therefore, is present only in IBM JVMs.

# 各种工具

http://stackoverflow.com/questions/13375105/thread-dump-analyzer

## visualvm

https://visualvm.java.net/

https://visualvm.java.net/download.html

visualvm_138.zip
https://java.net/downloads/visualvm/release138/visualvm_138.zip