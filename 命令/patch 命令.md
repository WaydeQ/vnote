# patch 命令
#### 利用以上命令，处理单个文件补丁的方法：

-  产生补丁
```
diff -uN file1 file2 >file.patch
​```

- 打补丁
```
patch -p0 < file.patch
​```

- 取消补丁
```
patch -RE -p0 < file.patch
1
2
3
4
5
6
7
8
```

对整个文件夹打补丁的情况：

# 产生补丁
diff -uNr dir1 dir2  >dir.patch
​
# 打补丁
cd dir2
patch -p1 < dir.patch
​
# 取消补丁
patch -R -p1 <dir.patch

例子：
[root@localhost script]# cat 1
111
222
333
[root@localhost script]# cat 2
000
111
222
333
444

[root@localhost script]# diff -uN 1 2 >3.patch
[root@localhost script]# patch -p0 < 3.patch 
patching file 1
[root@localhost script]# cat 1
000
111
222
333
444