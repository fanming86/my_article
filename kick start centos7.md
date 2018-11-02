---


---

<h2 id="、安装必要的软件">1、安装必要的软件</h2>
<p>xinetd  --tftpd的守护进程</p>
<p>tftp  —用于共享开机引导文件，（一般系统中自带）</p>
<p>nfs  —用于共享系统资源</p>
<p>syslinux  —生成pxelinux.0文件</p>
<p>dhcpd  ----自动分配ip</p>
<h2 id="、几个必要的目录">2、几个必要的目录</h2>
<p><strong>tftpboot</strong> —一般为tftp的默认路径 /var/lib/tftpboot</p>
<p>此路径存放用于引导的文件：initrd.img  pxelinux.0  vmlinuz  pxelinux.cfg</p>
<p><strong>/centos</strong>  ----此目录名称可以随意，只用于nfs共享iso镜像</p>
<p>可以直接将iso镜像挂载至此</p>
<pre><code>mount /dev/sr0 /centos
</code></pre>
<p><strong>/ksdir</strong>  ----用来放置ks.cfg文件</p>
<p>*这两个目录都需要共享出去，nfs共享的配置为：</p>
<pre><code>#cat /etc/exports

/centos *(ro,sync)

/ksdir *(ro,sync)
</code></pre>
<h2 id="、几个必要的文件">3、几个必要的文件</h2>
<p><strong>ks.cfg</strong> ----此文件是Linux系统自动安装的脚本文件，可以通过图形化工具kickstart来生成，此文件必须让开机时可以找到</p>
<p>安装图形化工具：<br>
<strong>yum -y install system-config-kickstart</strong></p>
<h4 id="pxelinux.0------">pxelinux.0  ----</h4>
<pre><code>yum install -y syslinux
rpm -qa | grep syslinux
rpm -ql syslinux | grep "pxelinux.0"
cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot
pxelinux.cfg/default  ----此文件是iso镜像文件中/centos/isolinux/isolinux.cfg文件
mkdir /var/lib/tftpboot/pxelinux.cfg &amp;&amp; cp /mnt/isolinux/isolinux.cfg /tftpboot/pxelinux.cfg/default
</code></pre>
<p><strong>vmlinuz</strong><br>
<strong>initrd.img</strong>  ----这两个文件在iso镜像中可以找到，</p>
<pre><code>cp /centos/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot
</code></pre>
<p><strong>最后，/var/lib/tftpboot 目录下的文件结构为：</strong></p>
<pre><code>[root@localhost tftpboot]# tree
.
├── initrd.img
├── pxelinux.0
├── pxelinux.cfg
│  └── default
└── vmlinuz
</code></pre>
<p><em><strong>default文件内容大致包含以下内容：</strong></em></p>
<pre><code>display boot.msg
menu background splash.png
menu title Welcome to CentOS 7.3 x86_64!
default linux
label linux
menu label ^Install CentOS Linux 7 x86_64
</code></pre>
<p><strong>menu default</strong> #注意，这里设置默认启动***（貌似在centos7中不起作用，所以在上方添加了 default linux）</p>
<p><strong>kernel vmlinuz</strong>  # 指定要启动的内核。同样要注意路径，默认是/tftpboot目录。</p>
<p><strong>append initrd=initrd.img ks=nfs://192.168.92.128/centos/ks.cfg</strong>  # 指定追加给内核的参数，initrd.img是一个最小的linux系统</p>
<p>ks文件中可以使用不同的文件共享服务器来进行配置，详细参考以下地址：</p>
<p><a href="https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/6/html/installation_guide/sn-automating-installation">https://access.redhat.com/documentation/zh-cn/red_hat_enterprise_linux/6/html/installation_guide/sn-automating-installation</a></p>
<p>ks文件：</p>

