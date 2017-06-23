---
layout: post
title: gitlab-jenkins-maven-CI自动部署
categories: 运维
description: gitlab-jenkins-maven-CI自动部署
---

## gitlab-jenkins-maven-CI自动部署

### 适用人群
> 开发、运维人员

> 目前的方案可以用于自动发布测试和生产环境代码，利用gitlab的Web Hook监听提交事件，然后再Jenkins的Job里匹配提交日志，符合要求的Push才会进行构建和发布

### 软件列表
```wiki
Linux CentOS6
jenkins 2.6.0.1
gitlab 7.6.1
maven 3.5.0
java OpenJDK-1.8.0
```
[Jenkins官网](https://jenkins.io/)
[Gitlab官网](https://about.gitlab.com/)
### 操作流程

#### 设置gitlab钩子
![Gitlab设置Web Hooks](/assets/achives/images/gitlab-hook.png)
> 注：如果没有公网IP，可以用内网穿透工具进行测试(推荐ngrok)

#### 安装Gitlab钩子插件
1. 下载：
[Jenkins-gitlab-hook插件](https://wiki.jenkins.io/display/JENKINS/Gitlab+Hook+Plugin)

2. 安装：”系统管理“ -> ”管理插件“ -> “高级” -> “上传插件” -> 重启

#### 创建Jenkins的Job
```wiki
1. 此时jenkins能接收到gitlab的事件了，想要监听gitlab发来的通知，就需要新建Project后，在项目配置中勾选 “源码管理” 如下图
```
![源码管理](/assets/achives/images/repo-config.jpeg)

注：示例中的Project是监听test分支上的事件(若配置了多个一样的Project则构建多个Project)，并且不要忽略了源码库浏览器的配置，因为在git pull完将会访问一次浏览器中配置的内容，若无法访问则直接报错导致构建失败

#### 配置Maven自动打包代码
这一项需要提前在系统中安装Maven然后在系统配置中添加Maven环境
![Maven配置](/assets/achives/images/maven-config.jpeg)
#### 配置发布代码
![SSH Publishers](/assets/achives/images/publish-config.jpeg)
> Source files 在当前Project目录下，选定需要发送的源文件，可以是[pattern](http://ant.apache.org/manual/dirtasks.html#patterns)通配符
> Remove prefix 去掉前缀目录结构，在目标服务器中只保留文件
> Remote directory 如果目录结构不存在，则创建
> Exec command 只能执行一条Shell，并且和Source files字段不同为空

方案一：
```wiki
发布思路：将maven打包后的war发布到远程服务器的目录下(这个目录在配置SSH连接信息时就有)，最后执行shell脚本将war覆盖到tomcat/webapps目录下
```
方案二：
```wiki
maven打包完后，直接执行本机shell脚本，具体打包逻辑由shell实现，这部分待实现
```

#### 配置构建失败后发送邮件
若配置不成功，则发送测试邮件时会打印如下异常信息
```wiki
com.sun.mail.smtp.SMTPSendFailedException: 501 mail from address must be same as authorization user;
```
查阅很多资料解释都看不清楚，这里的错误大致意思是：发送方账号和系统管理员账号不一致，答案很简单，就在配置邮箱页面中，搜索“系统管理员邮件地址”（中文版的情况下）即可，若搜索不到，也可以搜索“
Jenkins Location”
![邮箱配置](/assets/achives/images/email-config.png)
