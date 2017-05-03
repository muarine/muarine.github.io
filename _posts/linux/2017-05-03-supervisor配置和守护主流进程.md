---
layout: post
title: supervisor守护主流进程以及配置详解
categories: Linux
description: supervisor
---

supervisor守护主流进程以及配置详解

上一篇介绍了centos安装supervisor，[点击这里](/2017/04/11/Centos6.x安装supervisor并开机启动/)

### 配置说明
[官方](http://supervisord.org/configuration.html)
```config
; [program:xx]是被管理的进程配置参数，xx是进程的名称
[program:xx]
command=/opt/apache-tomcat-8.0.35/bin/catalina.sh run  ; 程序启动命令
autostart=true       ; 在supervisord启动的时候也自动启动
startsecs=10         ; 启动10秒后没有异常退出，就表示进程正常启动了，默认为1秒
autorestart=true     ; 程序退出后自动重启,可选值：[unexpected,true,false]，默认为unexpected，表示进程意外杀死后才重启
startretries=3       ; 启动失败自动重试次数，默认是3
user=tomcat          ; 用哪个用户启动进程，默认是root
priority=999         ; 进程启动优先级，默认999，值小的优先启动
redirect_stderr=true ; 把stderr重定向到stdout，默认false
stdout_logfile_maxbytes=20MB  ; stdout 日志文件大小，默认50MB
stdout_logfile_backups = 20   ; stdout 日志文件备份数，默认是10
; stdout 日志文件，需要注意当指定目录不存在时无法正常启动，所以需要手动创建目录（supervisord 会自动创建日志文件）
stdout_logfile=/opt/apache-tomcat-8.0.35/logs/catalina.out
stopasgroup=false     ;默认为false,进程被杀死时，是否向这个进程组发送stop信号，包括子进程
killasgroup=false     ;默认为false，向进程组发送kill信号，包括子进程
```

### 主流进程配置

##### redis
```config
command=/usr/local/bin/redis-server /etc/redis/6379.conf
stdout_logfile=/var/log/supervisor/redis.log
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
username=root
```
##### zookeeper
```config
[program:zookeeper]
command=/opt/soft/zookeeper-3.4.9/bin/zkServer.sh start-foreground
user=root
autostart=true
autorestart=true
startsecs=3
```

##### tomcat
```config
[program:tomcat01]
command=/home/rt/tomcat-8.0.29/bin/catalina.sh run
user=rt
autostart=true
autorestart=true
startsecs=3
```

##### nexus
```config
[program:nexus]
command=/home/ci/nexus-2.14.0-01/bin/nexus start
user=ci
autostart=true
autorestart=true
startsecs=3
```

##### confluence
```config
[program:confluence]
command=/home/ci/confluence-5.5.1/bin/catalina.sh run
user=ci
autostart=true
autorestart=true
startsecs=3
```
##### java process
```config
command=/opt/jdk1.7.0_79/bin/java -Dfile.encoding=UTF-8 -Djava.awt.headless=true -Djava.net.preferIPv4Stack=true -server -Xmx2g -Xms2g -Xmn256m -XX:PermSize=128m -Xss256k -XX:+DisableExplicitGC -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled -XX:+UseCMSCompactAtFullCollection -XX:LargePageSizeInBytes=128m -XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:+PrintGCDetails -Xloggc:./logs/gc.log -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=./logs/dump.log -classpath ./lib/*:web-test-2.0-SNAPSHOT.jar com.web.Application
directory=/home/app/web-test-2.0-SNAPSHOT/
autorestart=true
priority=10
user=app

stdout_logfile=/home/app/logs/supervisor/web/log.log
stdout_logfile_maxbytes=100MB
stdout_logfile_backups=10

stderr_logfile=/home/app/logs/supervisor/web/error.log
stderr_logfile_maxbytes=50MB
stderr_logfile_backups=10

redirect_stderr=false
```
