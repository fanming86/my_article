---


---

<p><strong>摘要</strong><br>
本文介绍了如何使用nagios通过nsclient监控windows主机。<br>
此次实验环境基于<a href="http://fanming.shop/2017/06/05/centos7InstallNagios/">上篇文章</a>，监控端在Linux环境下，Windows服务器使用的时windows server 2012。</p>
<p><strong>1. 安装NSClient++</strong><br>
访问http://nsclient.org/nscp/ 下载NSClient++，这里我使用的是NSCP-0.4.3.143-x64的msi安装包。下载之后直接安装，过程中会提示输入Nagios监控服务器地址和密码。地址填写正确，密码不用设置。Nagios监控服务器事先在监控Linux服务器时候已经搭建好。</p>
<p><strong>2.配置NSClient++</strong><br>
在 NSClient++ 的安装目录打开 nsclient.ini，将监控服务器的IP地址加到 allowed hosts 指令后面。<br>
<img src="http://img.blog.csdn.net/20180125175521714?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"><br>
然后检查下12489端口是否正常监听<br>
netstat -an | more</p>
<p>因为安装时候需要的模块已经勾选了，所以配置文件中不用再作设置。否则可以通过去掉每个模块前的;来启用该模块。</p>
<p>到这里即完成了Windows Server 2012上的配置。接下来我们设置Nagios监控服务器。</p>
<p><strong>3.Nagios监控服务器设置</strong><br>
vi /usr/local/nagios/etc/nagios.cfg<br>
搜索找到：<br>
Definitions for monitoring a Windows machine<br>
cfg_file=/usr/local/nagios/etc/objects/windows.cfg<br>
将第二行的#去掉打开Nagios监控的Windows模块，然后保存。<br>
<img src="http://img.blog.csdn.net/20180125180434354?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>
<p>接着编辑<br>
找到并设置监控的服务器：<br>
设置被监控主机名和被监控的Windows服务器地址。<br>
<strong>此地址为Linux服务端的ip地址</strong></p>
<pre><code>vi /usr/local/nagios/etc/objects/windows.cfg
define host{
use windows-server ; Inherit default values from a template
host_name winserver ; The name we're giving to this host
alias My Windows Server ; A longer name associated with the host
address 192.168.0.2 ; IP address of the host
}
</code></pre>
<p><img src="http://img.blog.csdn.net/20180126090631955?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>
<p>接下来搜索各种监控的服务并设置：</p>
<pre><code>#监控NSClient版本。
Create a service for monitoring the version of NSCLient++ that is installed
Change the host_name to match the name of the host you defined above

define service{
use generic-service
host_name winserver
service_description NSClient++ Version
check_command check_nt!CLIENTVERSION
}

#监控服务器在线时间。
Create a service for monitoring the uptime of the server
Change the host_name to match the name of the host you defined above

define service{
use generic-service
host_name winserver
service_description Uptime
check_command check_nt!UPTIME
}


#监控服务器CPU负载。
Create a service for monitoring CPU load
Change the host_name to match the name of the host you defined above

define service{
use generic-service
host_name winserver
service_description CPU Load
check_command check_nt!CPULOAD!-l 20,30,50
}


#监控服务器的内存占用。
 Create a service for monitoring memory usage
 Change the host_name to match the name of the host you defined above

define service{
use generic-service
host_name winserver
service_description Memory Usage
check_command check_nt!MEMUSE!-w 80 -c 90
}


#监控系统C盘的磁盘占用。
Create a service for monitoring C:\ disk usage
Change the host_name to match the name of the host you defined above

define service{
use generic-service
host_name winserver
service_description C:\ Drive Space
check_command check_nt!USEDDISKSPACE!-l c -w 80 -c 90
}
</code></pre>
<p>进入目录 /usr/local/nagios/libexec/<br>
在nagios监控端验证是否可以监控到windows主机，用命令./check_nt -H 192.168.13.132 -p 12489 -v UPTIME可以检测到Windows服务器已经启动的时长，也表示可以从Windows服务器拿到数据了。<br>
<img src="http://img.blog.csdn.net/20180126093055560?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>
<p>现在就可以登录到nagios的web界面，查看你的监控信息了<br>
<img src="http://img.blog.csdn.net/20180126093303518?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>

