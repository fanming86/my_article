liunx中使用touch命令更改文件的时间
------------------------
### 1. 首先介绍下Linux系统中文件的三个时间
	stat 命令可以查看文件的时间
```bash
[root@localhost ~]# stat test_rename 
  File: ‘test_rename’
  Size: 2193      	Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d	Inode: 17736275    Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Context: unconfined_u:object_r:mnt_t:s0
Access: 2019-03-19 18:38:39.361934126 +0800
Modify: 2019-03-19 18:37:16.572937478 +0800
Change: 2019-03-19 18:45:18.457917966 +0800
 Birth: -

```

- 1、访问时间，读一次这个文件的内容，这个时间就会更新。比如对这个文件运用 more、cat等命令。ls、stat命令都不会修改文件的访问时间。  

- 2、修改时间，修改时间是文件内容最后一次被修改时间。比如：vi后保存文件。ls -l列出的时间就是这个时间  

- 3、状态改动时间。是该文件的i节点最后一次被修改的时间，通过chmod、chown命令修改一次文件属性，这个时间就会更新。  

--------------------- 


###  2. touch的用法为：

	 touch [-actmd] 文件
	 参数：
	     -a:仅修改access time
	     -c:仅修改时间而不建立文件
	     -t:后面可以接时间，格式为：[[CC]YY]MMDDhhmm [.SS]
		 *该参数修改的是访问时间和修改时间*

				CC - 年份的前两位 
				YY - 年份的后两位 
				MM - 月份 [01-12]
				DD - 日期 [01-31]
				hh - 时 [00-23]
				mm - 分 [00-59]
				SS - 秒 [00-61]

	     -m:仅修改mtime
	     -d:后面可以接日期，也可以使用--date="日期或时间"

#### -a 的用法

```bash
[root@localhost ~]# touch -at 02101700 test_rename 
[root@localhost ~]# 
[root@localhost ~]# 
[root@localhost ~]# stat test_rename 
  File: ‘test_rename’
  Size: 2193      	Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d	Inode: 17736275    Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Context: unconfined_u:object_r:mnt_t:s0
Access: 2019-02-10 17:00:00.000000000 +0800
Modify: 2019-03-19 18:37:16.572937478 +0800
Change: 2019-03-19 22:22:42.166389810 +0800
 Birth: -
[root@localhost ~]# 
```

#### -m 的用法
```bash

[root@localhost ~]# touch -mt 02110700 test_rename 
[root@localhost ~]# 
[root@localhost ~]# stat test_rename 
  File: ‘test_rename’
  Size: 2193      	Blocks: 8          IO Block: 4096   regular file
Device: fd00h/64768d	Inode: 17736275    Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Context: unconfined_u:object_r:mnt_t:s0
Access: 2019-02-10 17:00:00.000000000 +0800
Modify: 2019-02-11 07:00:00.000000000 +0800
Change: 2019-03-19 22:23:34.486387692 +0800
 Birth: -
[root@localhost ~]# 



```
<!--stackedit_data:
eyJoaXN0b3J5IjpbOTk4NTYxMjAwXX0=
-->