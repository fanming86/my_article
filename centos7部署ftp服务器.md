1. 安装vsftpd  
　　1）安装vsftpd： yum install vsftpd -y  
　　2）启动vsftpd： systemctl start vsftpd  
　　3）设置开机启动：systemctl enable vsftpd  
 
 2. 创建用户和共享目录
	1）创建一个系统本地用户，指定该用户的家目录，并使其不能登录  
	```
	useradd -d /home/vsftp -s /sbin/nologin ftpuser  #创建用户     
	passwd ftpuser     #设置密码  
	```
    2）默认的ftp共享目录为/var/ftp，若要该为指定的目录，可设置anon_root和local_root参数，详情看下一步

 3. 设置配置文件：vim /etc/vsftpd/vsftpd.conf   
    //这里我们使用ftpuser用户的家目录作为本地用户访问的共享目录
    //使用/var/ftp 目录作为匿名用户访问的目录
    ````
    anonymous_enable=YES 　　　　      #允许匿名访问  
    anon_upload_enable=NO   　　　　   #上传  
    anon_mkdir_write_enable=NO  　　  #创建
    anon_other_write_enable=NO        #删除
    anon_root=/var/ftp 　　　　　　　　 #匿名访问目录
    local_root=/home/vsftp              #本地用户访问目录
    local_enable=YES      　　　　   	　# 允许使用本地帐户进行FTP用户登录验证
    write_enable=YES	　　　　　　　　#允许本地用户写入
    local_umask=022   　　　　　　　　   # 设置本地用户默认文件掩码022　　　　　　　
    ````
 4.  重启ftp服务，便可以访问ftp服务器了
