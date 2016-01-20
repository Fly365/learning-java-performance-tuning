tomcat中做Thread Dump的方法
=======

## 得到进程号

先得到tomcat的进程号，使用ps命令：

	[root@APP-TEST-DB2 ~]# ps -ef | grep tomcat

输出类似如下：

	tomcat   32327     1 58 09:48 ?        00:00:26 /usr/lib/jvm/java/bin/java -Xms256m -Xmx11000m -XX:PermSize=128m -XX:MaxPermSize=256m -Dorg.apache.catalina.SESSION_COOKIE_NAME=PPMoneySS -Dorg.apache.catalina.SESSION_PARAMETER_NAME=ppsession -Djavax.sql.DataSource.Factory=org.apache.commons.dbcp.BasicDataSourceFactory -classpath :/usr/share/tomcat6/bin/bootstrap.jar:/usr/share/tomcat6/bin/tomcat-juli.jar:/usr/share/java/commons-daemon.jar -Dcatalina.base=/usr/share/tomcat6 -Dcatalina.home=/usr/share/tomcat6 -Djava.endorsed.dirs= -Djava.io.tmpdir=/var/cache/tomcat6/temp -Djava.util.logging.config.file=/usr/share/tomcat6/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager org.apache.catalina.startup.Bootstrap start

这里32327就是进程号了。

## 执行dump命令

然后执行kill -3 命令多次，就可以得到多次thread dump，通常取三次。

	kill -3 32327

## 获取dump信息

上面的命令中可以看到tomcat安装的目录

	-Dcatalina.home=/usr/share/tomcat6

进入这个目录下的logs目录，找到最新的catalina-*****.out文件

	-rw-r--r--. 1 tomcat tomcat 106253435 1月  16 12:16 catalina-2016-01-16.out
	-rw-r--r--. 1 tomcat tomcat      2688 1月  20 09:48 catalina-2016-01-20.out

最新的这个catalina-2016-01-20.out文件中就会有Thread Dump信息。

用vi打开，输入"/Full thread dump"执行搜索命令，就会找到前面kill -3 得到的thread dump。

## 清理thread dump信息

上面的catalina-2016-01-20.out文件中可能有很多其他的out输出，文件可能很大，也可能之前的dump信息还在。因此如果想得到干净的dump信息，可以有两种方法：

1. 先清理干净再dump

	- 先执行service stop tomcat 之类的方法停止tomcat
	- 进入logs目录，执行rm -rf * 命令清理所有的日志文件
	- 执行service start tomcat 之类的方法启动tomcat
	- 然后再做thread dump

2. dump后删除不需要的内容

	- vi打开out文件，用前面说的输入"/Full thread dump"找到dump信息
	- 光标上移一行
	- 输入":1,.d", 这样vi会将从第一行到这行的所有内容都删除
	- 输入":x"保存退出，现在out文件里面dump之前的内容都被清理了

