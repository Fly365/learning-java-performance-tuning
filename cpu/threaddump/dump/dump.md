获取Thread Dump
==============

## Thread Dump是什么？

A Java thread dump is the one of the traces/dumps that JVM provides to help diagnosis a hang, deadlock, or monitor contention issue. It contains diagnostic information related to the JVM and a Java application captured at a point during execution. For example, the information can be a list of all the threads that run on a Java virtual machine. Usually thread dumps have more than just thread information. Thread dumps can produce information about the operating system, application environment, threads, stacks, locks, and memory. 

## 注意事项

Thread Dump 时的注意事项：

1. 不同的JAVA虚拟机的Thread Dump的创建方法和文件格式是不一样的，不同的JVM版本， dump信息也有差别。

2. 单独一次的thread dump的信息，通常不足以确认问题。实际使用时建议连续收集三次dump信息，以排除偶然情况。