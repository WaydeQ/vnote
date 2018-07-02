# screen

### 安装
```
yum install screen -y
```

#### 新建一个窗口
```
screen -S yourname
```

#### 常用命令
```
screen -S yourname -> 新建一个叫yourname的session
screen -ls -> 列出当前所有的session
screen -r yourname -> 回到yourname这个session
screen -d yourname -> 远程detach某个session
screen -d -r yourname -> 结束当前session并回到yourname这个session
```

#### 查看窗口
```
screen -ls
There is a screen on:
        16582.pts-1.tivf06      (Detached)
1 Socket in /tmp/screens/S-root.
```

#### 重新连接会话：
```
screen -r 16582
```