# find命令
### 查找目录下包含某ip的文件，替换
```
find . -type f -name "*" | xargs grep "10.66.117.144" |awk -F ":" '{print $1}'|sort -u  |grep -vE "tmp|log"

find . -type f -name '*' |xargs grep -s "10.148.0.2" |awk -F ":" '{print $1}'|sort -u  |grep -vE "tmp|log|SourceCode|2017"

find . -type f -name '*' |xargs grep "10.148.0.10" |awk -F ":" '{print $1}'|sort -u  |grep -vE "tmp|log"|xargs sed -i 's/10.148.0.10/172.16.56.7/g'
```

#### 以:="; 为分割查找字符
```
awk -F '[:=";]' '{print $21,$55}' test
```

#### 批 量 注 释 :
-  按行替换
```
10,20s/ ^ / # / g
```

-  全篇查找替换 
```
%s/aaa/bbb/g
```
