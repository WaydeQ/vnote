# docker crontab 不执行问题
```
yum install vixie-cron crontabs -y

vim /etc/pam.d/crond
#session required pam_loginuid.so
crontab -e
* * * * * * ping www.baidu.com

ps aux | grep ping 发现不运行
tail -f /var/log/cron 没有日志

vim /etc/rsyslog.conf
找到这行，开启 .info;mail.none;authpriv.none;cron.none -/var/log/messages

#重启生效
/etc/init.d/rsyslog restart 

tail -f /var/log/cron
#报错信息
Feb 17 11:52:01 383096aa9261 crond[29583]: (root) FAILED to open PAM security session (无法为指定的会话创建
／去除项。)

vim /etc/pam.d/crond
注释#session required pam_loginuid.so

### 目录
/var/spool/cron
```