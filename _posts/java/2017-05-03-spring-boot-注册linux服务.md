---
layout: post
title: spring-boot-注册linux服务
categories: Java
description: spring-boot
---

### 配置pom.xml

```xml
<plugin>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <mainClass>cn.xxx.BossStarter</mainClass>
        <classifier>package</classifier>
        <executable>true</executable>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>repackage</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```
### 创建软链接
```shell
ln -s /opt/xxx-package.jar /etc/init.d/xxx-package
ll /etc/init.d/
```

### 开机自启

```shell
chkconfig --add xxx-package
chkconfig xxx-package on
chkconfig --list

service xxx-package start
service xxx-package stop
```
