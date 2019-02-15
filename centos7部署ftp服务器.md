> 本文由fanming首发于个人博客 www.ifanm.com  转载无需联系作者，但请注明出处。
 
## 前言  
 ----
> FTP 是File Transfer Protocol（文件传输协议）的英文简称。  
> 用于Internet上的控制文件的双向传输。  
> 同时，它也是一个应用程序（Application）。  
> 基于不同的操作系统有不同的FTP应用程序，而所有这些应用程序都遵守同一种协议以传输文件。  
-------
### vsftp提供了以下三种登陆方式  
#### 匿名用户登陆anonymous  
> 使用anonymous是应用广泛的一种FTP服务器.如果用户在FTP服务器上没有帐号,那么用户可以以anonymous为用户名,以自己的电子邮件地址为密码进行登录.当匿名用户登录FTP服务器后,其登录目录为匿名FTP服务器的根目录/var/ftp.为了减轻FTP服务器的负载,一般情况下,应关闭匿名帐号的上传功能.  

#### 虚拟用户登陆guest    
> 如果用户在FTP服务器上拥有帐号,但此帐号只能用于文件传输服务,那么该帐号就是guest,guest是真实帐号的一种形式,它们的不同之处在于,geust登录FTP服务器后,不能访问除宿主目录以外的内容.  

#### 本地用户登陆real    
> real也称为本地帐号,就是以真实的用户名和密码进行登录,但前提条件是用户在FTP服务器上拥有自己的帐号.用真实帐号登录后,其登录的目录为用户自己的目录,该目录在系统建立帐号时系统就自动创建/etc/passwd文件中的用户

----

### 1、安装vsftpd  
```bash
#安装vsftpd
[root@client /]# yum install vsftpd -y
#启动vsftpd
[root@client /]# systemctl start vsftpd
#设置开机启动
[root@client /]# systemctl enable vsftpd
#开放防火墙规则
[root@client /]# firewall-cmd --add-service=ftp --permanent
[root@client /]# firewall-cmd --reload
```
##### 在本机进行测试，使用匿名用户登录测试
此服务正常启动后，可以打开浏览器并在地址栏中输入：ftp://localhost；此时应该可以看到共享的目录 pub ；默认情况下共享目录为/var/ftp。

### 2、配置文件中的公共配置
主配置文件：vim /etc/vsftpd/vsftpd.conf   

```bash
write_enable=YES	 	#是否允许用户具有在FTP服务器文件中执行写的权限,默认是no
download_enable=YES 	#设置用户是否有下载的权限，默认yes
dirmessage_enable=YES 	#激活目录信息,当远程用户更改目录时,将出现提示信息
xferlog_enable=YES 		#启用上传和下载日志功能
xferlog_file=/var/log/xferlog	#设置日志存放的位置
listen_port=4449	#修改服务端口
```

### 3、针对匿名用户的配置   
/var/ftp 目录作为匿名用户访问的目录  
```bash
anonymous_enable=YES 　　　　      #	#是否允许匿名访问  
anon_upload_enable=NO   　　　　   #上传  
anon_mkdir_write_enable=NO  　　  #创建  
anon_other_write_enable=NO        #删除  
anon_root=/var/ftp 　　　　　　　　 #匿名访问目录  
local_root=/home/vsftp            #本地用户访问目录  
local_enable=YES      　　　　    # 允许使用本地帐户进行FTP用户登录验证  
```
### 4、针对本地用户的配置
1）创建一个系统本地用户，并使其不能登录  
```bash
#创建用户 
[root@client /]# useradd -s /sbin/nologin ftpuser  
#设置密码
[root@client /]# passwd ftpuser       
#在根目录下创建目录public，用来共享
[root@client /]# mkdir /public       
```
2）更改配置文件
这里我们使用/public 目录作为本地用户访问的共享目录  

```bash
local_enable=YES      #是否允许使用本地帐户登录 
local_root=/public    #本地用户访问目录  
local_umask=022   　　#设置本地用户默认文件掩码022　　　
chroot_local_user=YES #可以将用户锁定在共享目录中　　　　
```

### 5、重启ftp服务，便可以访问ftp服务器了

##### 使用本地用户登录 [ftp://user:password@192.168.13.128](ftp://user:password@192.168.13.128) #在浏览器中使用本地用户登录

![](https://ws1.sinaimg.cn/large/006h9k0Tly1fwkmoued00j30g503uq37.jpg)


##### 使用匿名用户登录  [ftp://192.168.13.128](ftp://192.168.13.128) #匿名用户访问，直接访问地址即可

![](https://ws1.sinaimg.cn/large/006h9k0Tly1fwkmqdxf3hj30g803zaac.jpg)
