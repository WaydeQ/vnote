# sudo问题
```
sorry, you must have a tty to run sudo
sudo: sorry, you must have a tty to run sudo这个情况，其实修改一下sudo的配置就好了
vi /etc/sudoers (最好用visudo命令)
注释掉 Default requiretty 一行
#Default requiretty
意思就是sudo默认需要tty终端。注释掉就可以在后台执行了。
```