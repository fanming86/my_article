---


---

<h2 id="nagios简介"><strong>1. nagios简介</strong></h2>
<pre><code>	Nagios是一款开源的免费网络监视工具，能有效监控Windows、Linux和Unix的主机状态，交换机路由器等网络设置，打印机等。在系统或服务状态异常时发出邮件或短信报警第一时间通知网站运维人员，在状态恢复后发出正常的邮件或短信通知。
	    
	Nagios 可以监控的功能有：
	1、监控网络服务（SMTP、POP3、HTTP、NNTP、PING等）； 
	2、监控主机资源（处理器负荷、磁盘利用率等）；
	3、简单地插件设计使得用户可以方便地扩展自己服务的检测方法；
	4、并行服务检查机制；
	5、具备定义网络分层结构的能力，用"parent"主机定义来表达网络主机间的关系，这种关系可被用来发现和明晰主机宕机或不可达状态；
	6、当服务或主机问题产生与解决时将告警发送给联系人（通过EMail、短信、用户定义方式）；
	7、具备定义事件句柄功能，它可以在主机或服务的事件发生时获取更多问题定位；
	8、自动的日志回滚；
	9、可以支持并实现对主机的冗余监控；
	10、可选的WEB界面用于查看当前的网络状态、通知和故障历史、日志文件等。
</code></pre>
<h2 id="安装nagios基础支持套件和添加用户"><strong>2. 安装Nagios基础支持套件和添加用户</strong></h2>
<p>1).安装套件</p>
<pre><code> yum install gcc glibc glibc-common gd gd-devel xinetd openssl-devel
</code></pre>
<p>2).创建nagios用户和用户组(先进行该步骤，不然编译nagios时会报错)</p>
<pre><code>useradd -s /sbin/nologin nagios   #创建nagios用户，设置该用户无法登录
mkdir /usr/local/nagios   
chown -R nagios.nagios /usr/local/nagios   #设置目录权限
</code></pre>
<p>3).安装httpd和php</p>
<pre><code>yum install httpd*    ##安装Web服务器Apache相关服务,nagios提供了web监控界面，通过web界面可以看到被监控主机运行状态
yum install php*      ##安装PHP相关服务
</code></pre>
<h2 id="下载并编译安装nagios"><strong>3. 下载并编译安装Nagios</strong></h2>
<p>1).下载并解压nagios<br>
可以前往 <a href="https://www.nagios.org">https://www.nagios.org</a> 获取最新的软件包，这里以 Nagios 核心 3.4.3 为例：</p>
<pre><code>wget https://assets.nagios.com/downloads/nagioscore/releases/nagios-3.4.3.tar.gz 
tar -zxvf nagios-3.4.3.tar.gz 
cd nagios   #编译安装nagios
</code></pre>
<p>2).编译安装nagios</p>
<pre><code>./configure --prefix=/usr/local/nagios
make all   ##中间如有选择，直接A
make install    ##安装主程序，CGI和HTML文件
make install-init   ##在/etc/rc.d/init.d安装启动脚本
make install-config    ##安装示例配置文件
make install-commandmode    ##配置目录权限
</code></pre>
<p>3).配置自启动</p>
<pre><code>chkconfig --add nagios   ##增加nagios为系统服务
chkconfig --level 35 nagios on  ##设置nagios在运行级别3、5的情况下为开启状态
chkconfig --list nagios   ##列出nagios服务设置详情
</code></pre>
<p>4).验证程序是否配置正确</p>
<pre><code>/usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg
</code></pre>
<p><img src="http://img.blog.csdn.net/20180123112026072?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>
<h2 id="安装nagios插件"><strong>4. 安装Nagios插件</strong></h2>
<p>1).下载nagios-plugins<br>
可以前往 <a href="https://www.nagios.org">https://www.nagios.org</a> 获取最新的软件包，这里以 插件 2.0.3 为例：</p>
<pre><code>wget http://nagios-plugins.org/download/nagios-plugins-2.0.3.tar.gz  ##下载nagios-plugins
tar -zxvf nagios-plugins-2.0.3.tar.gz   ##解压nagios-plugins
cd nagios-plugins-2.0.3
</code></pre>
<p>2).编译安装nagios-plugins</p>
<pre><code>./configure --prefix=/usr/local/nagios
make
make install
</code></pre>
<p>3).下载nagios-snmp-plugins</p>
<pre><code>wget http://nagios.manubulon.com/nagios-snmp-plugins.1.1.1.tgz 
tar -zxvf nagios-snmp-plugins.1.1.1.tgz 
cd nagios-plugins-2.0.3/
</code></pre>
<p>4).编译安装nagios-snmp-plugins</p>
<pre><code>yum install perl-CPAN   ##yum安装Perl-CPAN，CPAN是Perl软件收藏库，收集了大量有用的Perl模块(modules)及其相关的文件。nagios-snmp-plugins是一套用Perl编写的通过SNMP方式监控主机的插件程序，因此需要先安装perl-CPAN
perl -MCPAN -e shell
cpan[1]&gt; install Net::SNMP
</code></pre>
<h2 id="配置httpd"><strong>5. 配置httpd</strong></h2>
<p>1).</p>
<pre><code>vi /etc/httpd/conf/httpd.conf
将
User apache 
Group apache
</code></pre>
<pre><code>修改为如下内容：
</code></pre>
<p><img src="http://img.blog.csdn.net/20180123104741286?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述"></p>
<p>2).在如下位置添加index.php<br>
<img src="http://img.blog.csdn.net/20180123104930999?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="fanming.shop"></p>
<p>3).配置nagios.conf</p>
<pre><code>cd nagios/       #nagios 解压后的目录
make install-webconf    ##安装nagios的web接口,会在/etc/httpd/conf.d目录中创建nagios.conf文件
vim /etc/httpd/conf.d/nagios.conf
</code></pre>
<pre><code>将22行和39行附近的htpasswd.user改为htpasswd（注:也可不进行更改，但在下部中要加全路径名）
</code></pre>
<p><img src="http://img.blog.csdn.net/20180123105742740?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzU2MjYyNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="fanming.shop"></p>
<p>4).创建nagios验证文件</p>
<pre><code>htpasswd -c /usr/local/nagios/etc/htpasswd admin  ##创建nagios登录web界面的用户名和密码
cat /usr/local/nagios/etc/htpasswd    ##查看认证文件的内容
</code></pre>
<p>5).关闭SELinux和防火墙：</p>
<pre><code>a、临时关闭（不用重启机器）：
setenforce 0    ##设置SELinux 成为permissive模式  （关闭SELinux）
setenforce 1    ##设置SELinux 成为enforcing模式    (开启SELinux）
b、修改配置文件需要重启机器：
vi /etc/selinux/config
将SELINUX=enforcing 改为SELINUX=disabled
需重启机器
c、可自己做策略开放相应端口，这里我直接关闭:
systemctl status firewalld.service       ##查看防火墙状态
systemctl stop firewalld.service         ##关闭防火墙
</code></pre>
<p>6).启用服务并进入nagios</p>
<pre><code>systemctl start httpd.service
systemctl start snmpd.service
systemctl start nagios.service
</code></pre>
<p><strong>至此，就可以访问http://localhost/nagios 访问web界面了</strong></p>

