## 1、安装必要的软件

xinetd  --tftpd的守护进程

tftp  ---用于共享开机引导文件，（一般系统中自带）

nfs  ---用于共享系统资源

syslinux  ---生成pxelinux.0文件

dhcpd  ----自动分配ip

## 2、几个必要的目录

**tftpboot** ---一般为tftp的默认路径 /var/lib/tftpboot

此路径存放用于引导的文件：initrd.img  pxelinux.0  vmlinuz  pxelinux.cfg

**/centos**  ----此目录名称可以随意，只用于nfs共享iso镜像

可以直接将iso镜像挂载至此

    mount /dev/sr0 /centos

**/ksdir**  ----用来放置ks.cfg文件

*这两个目录都需要共享出去，nfs共享的配置为：

    #cat /etc/exports
    
    /centos *(ro,sync)
    
    /ksdir *(ro,sync)

## 3、几个必要的文件

**ks.cfg** ----此文件是Linux系统自动安装的脚本文件，可以通过图形化工具kickstart来生成，此文件必须让开机时可以找到

安装图形化工具：  
**yum -y install system-config-kickstart**
#### pxelinux.0  ----

    yum install -y syslinux
    rpm -qa | grep syslinux
    rpm -ql syslinux | grep "pxelinux.0"
    cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot
    pxelinux.cfg/default  ----此文件是iso镜像文件中/centos/isolinux/isolinux.cfg文件
    mkdir /var/lib/tftpboot/pxelinux.cfg && cp /mnt/isolinux/isolinux.cfg /tftpboot/pxelinux.cfg/default

 **vmlinuz**
**initrd.img**  ----这两个文件在iso镜像中可以找到，

    cp /centos/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot

**最后，/var/lib/tftpboot 目录下的文件结构为：**

    [root@localhost tftpboot]# tree
    .
    ├── initrd.img
    ├── pxelinux.0
    ├── pxelinux.cfg
    │  └── default
    └── vmlinuz

***default文件内容大致包含以下内容：***

	   display boot.msg
	   menu background splash.png
	   menu title Welcome to CentOS 7.3 x86_64!
	   default linux
	   label linux
	   menu label ^Install CentOS Linux 7 x86_64

**menu default** #注意，这里设置默认启动***（貌似在centos7中不起作用，所以在上方添加了 default linux）

**kernel vmlinuz**  # ==指定要启动的内核==。同样要注意路径，默认是/tftpboot目录。

**append initrd=initrd.img ks=nfs://192.168.92.128/centos/ks.cfg**  # 指定追加给内核的参数，initrd.img是一个最小的linux系统

ks文件中可以使用不同的文件共享服务器来进行配置，详细参考以下地址：

[https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/6/html/installation_guide/sn-automating-installation](https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/6/html/installation_guide/sn-automating-installation)

ks文件：



