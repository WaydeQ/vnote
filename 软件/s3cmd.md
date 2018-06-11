# s3cmd
1) 注册 Amazon AWS / S3 
首先得注册S3服务，这个一般账号创建完毕之后应该都有，然后创建用户，这时会生成 
你的Access Key and Secret Key，下载保存好备用 
这个key对是你通过命名行工具控制AWS资源的信任验证 
当你具有AWS某项权限之后，就可以通过你的keys命令行控制了

2) 安装s3cmd并设置好 
下载下来之后，直接解压就可以使用其中的二进制可执行文件了 
你可以安装到系统中去，这样在任何目录下都可以直接使用s3cmd 
安装：

python setup.py install
(注意此处的python必须是2.6版本以上） 
或者使用pip来安装，如果没有pip命令，就

yum -y install python-pip
然后pip install s3cmd
然后设置

s3cmd --configure
执行s3cmd –configure之后会提示你输入你的Access Key和 Secret Key 
然后会提示时候进行上传测试，测试OK之后环境设置就OK 
然后保存设置

3) s3cmd mb创建新Bucket（即S3容器）

s3cmd mb s3://my-new-bucket-name
s3cmd ls s3://my-new-bucket-name/*
s3cmd ls 该命令可以list出你当前的S3 bucket

4) 上传文件到bucket:

上传单个文件

~$ s3cmd put some-file.xml s3://public.s3tools.org/somefile.xml
some-file.xml -> s3://public.s3tools.org/somefile.xml [1 of 1]
123456 of 123456 100% in 2s 51.75 kB/s done
上传多个目录到bucket:

~$ s3cmd put --recursive dir1 dir2 s3://public.s3tools.org/somewhere/
File 'dir1/file1-1.txt' stored as 's3://public.s3tools.org/somewhere/dir1/file1-1.txt' [1 of 5]
...
目标目录不用提前创建，上传时会自动创建

5) 从S3 bucket上下载文件 
下载单个文件

~$ s3cmd get s3://public.s3tools.org/somefile.xml some-file-2.xml
s3://public.s3tools.org/somefile.xml -> some-file-2.xml [1 of 1]
123456 of 123456 100% in 3s 35.75 kB/s done
下载整个目录

~$ s3cmd get --recursive s3://public.s3tools.org/somewhere
File s3://public.s3tools.org/somewhere/dir1/file1-1.txt saved as './somewhere/dir1/file1-1.txt'
...
注意下面两条命名的区别 
get s3://public.s3tools.org/somewhere 
get s3://public.s3tools.org/somewhere/

第一条命令下载下来的东西会是/somewhere/dir1/** 即连同somewhere及其子目录一并下载，并在本地创建相同的目录结构 
第二条下载下来的没有somewhere目录，即指下载该目录的内容，在本地并不创建somewhere目录，一般会使用recursive参数

~$ s3cmd get --recursive s3://public.s3tools.org/somewhere/*
来下载该目录下所有内容，包括子目录

如果你指定下载存放目录，结果会是这样的

~$ s3cmd get --recursive s3://public.s3tools.org/somewhere /tmp
File s3://public.s3tools.org/somewhere/dir1/file1-1.txt saved as '/tmp/dir1/file1-1.txt'
...
6) 删除操作:

删除 s3://public.s3tools.org/somewhere/下的所有内容

~$ s3cmd del --recursive s3://public.s3tools.org/somewhere/
File s3://public.s3tools.org/somewhere/dir1/file1-1.txt deleted
...
删除整个S3 bucket

~$ s3cmd rb s3://public.s3tools.org
ERROR: S3 error: 409 (BucketNotEmpty): The bucket you tried to delete is not empty
注意删除整个S3 bucket之前你必须确保它是空的 
确保是空的bucket之后用上面的命令删除bucket 
或者强行删除 - -force

7) 同步操作

1、同步当前目录下所有文件

~$ s3cmd sync ./ s3://my-bucket-name/
2、加 ” - -delete-removed”参数后，会删除本地不存在的文件。

~$ s3cmd sync --delete-removed ./ s3://my-bucket-name/
3、加 ” - -skip-existing”参数后，不进行MD5校验，直接跳过本地已存在的文件。

~$ s3cmd sync --skip-existing ./ s3://my-bucket-name/
8) 高级同步操作

1、排除、包含规则（- -exclude 、- -include）

~$ s3cmd sync --exclude '*.doc' --include 'dir2/*' ./ s3://my-bucket-name/
2、从文件中载入排除或包含规则。(- -exclude-from、- -include-from)

~$ s3cmd sync --exclude-from exclude.txt ./ s3://my-bucket-name/
exclude.txt 文件内容:

# comments here
*.jpg
*.gif
3、排除或包含规则支持正则表达式

--rexclude 、--rinclude、--rexclude-from、--rinclude-from