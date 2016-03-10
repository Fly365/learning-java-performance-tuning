查看和监控CPU
==========




# Windows

windows下最常用的CPU使用率监控工具自然是操作系统自带的Task Manager/任务管理器和performance Monitor/性能监控器。

## 任务管理器

> 注：貌似这个不用学

提醒：必须勾选Windows Task Manager的View(查看)菜单上的 Show Kernal Utilization(查看内核时间)才能显示系统态CPU使用率。

对于windows10,View菜单中没有这个选项，需要在右侧CPU图形上右键，然后选择"显示内核时间".

另外右键菜单中，在"将图形更改为"中可以选择"总体利用率"/"逻辑处理器"(即单独实现所有逻辑核心)/"NUMA节点"(按CPU个数分别显示)。

## 性能监控器

在windows程序列表的"windows 管理工具"中可以找到这个性能监控器。

右键，添加计数器，通常选择"processor time"/"user time"和"privileged time"(即系统态/内核态).

右键菜单中，选择"属性/properties"，点击"图标/graph"，可以选择各种展示形式。

## Windows Typeperf

Windows Typeperf是用来收集操作系统性能统计数据的命令行。

相关资料：

- [微软网站的Typeperf介绍](https://technet.microsoft.com/en-us/library/bb490960.aspx)
- [利用TypePerf.exe](http://www.cnblogs.com/qanholas/archive/2011/06/18/2084455.html)

# Linux

## 图形界面

### GNOME system monitor

GNOME系统监控器，可以用命令 gnome-system-monitor 命令启动，有gnome do则直接在gnome do中简单输入"system monitor"。

### xosview

xosview可以通过类似的命令安装：

	sudo apt-get install xosview

xosview的一个重要特性是可以将CPU使用率进一步分为用户态，系统态和空闲CPU。

## 命令行

更多时候还是需要用到命令行工具。

### vmstat

vmstat命令输入如下：

    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
     r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
     1  0      0 12565540 119236 2047156    0    0    13    17   70  120  2  1 97  1







