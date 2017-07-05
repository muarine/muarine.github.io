---
layout: post
title: CentOS-6.x 安装Supervisor并开机启动
categories: Linux
description: supervisor
---


CentOS安装vsftpd服务和分配账户权限

### 需求
```wiki
现需要提供FTP文件上传服务，并分配不同的FTP账户，并分配账户目录互不干涉
```

### 安装vsfpd
```shell
yum update -y
yum install vsftpd -y
```

### 修改vsftpd.conf
```conf
# Example config file /etc/vsftpd/vsftpd.conf
#
# The default compiled in settings are fairly paranoid. This sample file
# loosens things up a bit, to make the ftp daemon more usable.
# Please see vsftpd.conf.5 for all compiled in defaults.
#
# READ THIS: This example file is NOT an exhaustive list of vsftpd options.
# Please read the vsftpd.conf.5 manual page to get a full idea of vsftpd's
# capabilities.
#
# Allow anonymous FTP? (Beware - allowed by default if you comment this out).
anonymous_enable=NO
#
# Uncomment this to allow local users to log in.
local_enable=YES
#
# Uncomment this to enable any form of FTP write command.
write_enable=YES
#
# Default umask for local users is 077. You may wish to change this to 022,
# if your users expect that (022 is used by most other ftpd's)
local_umask=022
#
# Uncomment this to allow the anonymous FTP user to upload files. This only
# has an effect if the above global write enable is activated. Also, you will
# obviously need to create a directory writable by the FTP user.
anon_upload_enable=NO
#
# Uncomment this if you want the anonymous FTP user to be able to create
# new directories.
anon_mkdir_write_enable=YES
#
# Activate directory messages - messages given to remote users when they
# go into a certain directory.
dirmessage_enable=YES
#
# The target log file can be vsftpd_log_file or xferlog_file.
# This depends on setting xferlog_std_format parameter
xferlog_enable=YES
#
# Make sure PORT transfer connections originate from port 20 (ftp-data).
connect_from_port_20=YES
#
# If you want, you can arrange for uploaded anonymous files to be owned by
# a different user. Note! Using "root" for uploaded files is not
# recommended!
#chown_uploads=YES
#chown_username=whoever
#
# The name of log file when xferlog_enable=YES and xferlog_std_format=YES
# WARNING - changing this filename affects /etc/logrotate.d/vsftpd.log
xferlog_file=/var/log/vsftpd.log
#
# Switches between logging into vsftpd_log_file and xferlog_file files.
# NO writes to vsftpd_log_file, YES to xferlog_file
xferlog_std_format=YES
#
# You may change the default value for timing out an idle session.
#idle_session_timeout=600
#
# You may change the default value for timing out a data connection.
#data_connection_timeout=120
#
# It is recommended that you define on your system a unique user which the
# ftp server can use as a totally isolated and unprivileged user.
#nopriv_user=ftpsecure
#
# Enable this and the server will recognise asynchronous ABOR requests. Not
# recommended for security (the code is non-trivial). Not enabling it,
# however, may confuse older FTP clients.
#async_abor_enable=YES
#
# By default the server will pretend to allow ASCII mode but in fact ignore
# the request. Turn on the below options to have the server actually do ASCII
# mangling on files when in ASCII mode.
# Beware that on some FTP servers, ASCII support allows a denial of service
# attack (DoS) via the command "SIZE /big/file" in ASCII mode. vsftpd
# predicted this attack and has always been safe, reporting the size of the
# raw file.
# ASCII mangling is a horrible feature of the protocol.
ascii_upload_enable=YES
ascii_download_enable=YES
#
# You may fully customise the login banner string:
ftpd_banner=Welcome to Rtmap FTP service.
#
# You may specify a file of disallowed anonymous e-mail addresses. Apparently
# useful for combatting certain DoS attacks.
#deny_email_enable=YES
# (default follows)
#banned_email_file=/etc/vsftpd/banned_emails
#
# You may specify an explicit list of local users to chroot() to their home
# directory. If chroot_local_user is YES, then this list becomes a list of
# users to NOT chroot().
chroot_local_user=YES
# chroot_list_enable=YES
# (default follows)
# chroot_list_file=/etc/vsftpd/chroot_list
# allow_writeable_chroot=YES
#
# You may activate the "-R" option to the builtin ls. This is disabled by
# default to avoid remote users being able to cause excessive I/O on large
# sites. However, some broken FTP clients such as "ncftp" and "mirror" assume
# the presence of the "-R" option, so there is a strong case for enabling it.
#ls_recurse_enable=YES
#
# When "listen" directive is enabled, vsftpd runs in standalone mode and
# listens on IPv4 sockets. This directive cannot be used in conjunction
# with the listen_ipv6 directive.
listen=YES
#
# This directive enables listening on IPv6 sockets. To listen on IPv4 and IPv6
# sockets, you must run two copies of vsftpd with two configuration files.
# Make sure, that one of the listen options is commented !!
#listen_ipv6=YES

pam_service_name=vsftpd
userlist_enable=YES
userlist_file=/etc/vsftpd/user_list
tcp_wrappers=YES

guest_enable=YES
# 这里需要建立vsftpd的账户,并指定账户作用目录
guest_username=ftpuser
user_config_dir=/etc/vsftpd/vuser_config
#anon_other_write_enable=YES

# 开启被动模式 防火墙用
pasv_enable=YES
pasv_min_port=30000
pasv_max_port=31000
```

### 添加FTP账户

#### 准备工作

```shell
cd /etc/vsftpd/
mkdir vuser_config

groupadd ftpuser
useradd -g ftpuser ftpuser

yum install db4-utils
touch vuser_passwd.txt

```
#### 配置FTP账户信息
```shell
# vim /etc/vsftpd/vuser_passwd.txt
# 用户名和密码各起一行，多个账户交替输入即可
ftpuser
passwd
u_BU
passwd
```
#### 生成FTP密码对

```shell
db_load -T -t hash -f vuser_passwd.txt vuser_passwd.db
chmod 600 /etc/vsftpd/vuser_passwd.db
```
#### 配置/etc/pam.d/vsftpd

```shell
# vim /etc/pam.d/vsftpd

#%PAM-1.0
#session    optional     pam_keyinit.so    force revoke
#auth       required    pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed
#auth       required    pam_shells.so
#auth       include     password-auth
#account    include     password-auth
#session    required     pam_loginuid.so
#session    include     password-auth


auth required pam_userdb.so db=/etc/vsftpd/vuser_passwd
account required pam_userdb.so db=/etc/vsftpd/vuser_passwd
```

### 防火墙配置
```conf
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
# 允许端口
-A  INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A  INPUT -p tcp -m multiport --dport 20,21  -m state --state NEW -j ACCEPT
-A  INPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT
-A INPUT -p tcp --dport 30000:31000 -j ACCEPT
#允许ip访问端口

#其余端口不开放
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
```

### ftpuser禁用SSH登录
```shell
usermod -s /sbin/nologin ftpuser

chkconfig vsftpd on
service vsftpd restart
```
