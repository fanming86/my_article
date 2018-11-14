---


---

<p>在Redhat7系列系统中配置网络，此教程适用于cento7。此实验环境使用VMware虚拟机安装的Redhat7.</p>
<p>由于使用的时VMware虚拟机，我们直接使用虚拟机的默认设置：<br>
<img src="https://ws1.sinaimg.cn/large/006h9k0Tly1fx21obxaljj30a30ckmxj.jpg" alt=""></p>
<p>关于VMware中的几个连接模式，此文不做过多介绍。如果需要连接互联网，请使用nat模式或者桥接模式。这里我使用nat模式实验</p>
<h2 id="、进到系统后，可以使用命令-ifconfig来查看此机器的网卡和ip信息">1、进到系统后，可以使用命令 <code>ifconfig</code>来查看此机器的网卡和IP信息</h2>
<p><img src="https://ws1.sinaimg.cn/large/006h9k0Tly1fx223fqhdgj30ni0e9wg7.jpg" alt=""><br>
默认情况下应该能看到这三个网卡，其中，ens33是我们要使用的以太网卡，lo是本地回环地址，virbr0是用于和虚拟机通讯的（此虚拟机指Linux系统中的虚拟机）</p>
<table><tbody><tr><td bgcolor="orange">这里如果没有ifconfig这条命令，可以使用：yum install net-tools  来解决</td></tr></tbody></table>
<h2 id="、下面正式配置网络地址">2、下面正式配置网络地址</h2>
<h3 id="关于网卡的配置文件的目录：">2.1关于网卡的配置文件的目录：</h3>
<pre class=" language-bash"><code class="prism  language-bash">/etc/sysconfig/network-scripts
</code></pre>
<hr>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@localhost ~<span class="token punctuation">]</span><span class="token comment"># cd  /etc/sysconfig/network-scripts/</span>
<span class="token punctuation">[</span>root@localhost network-scripts<span class="token punctuation">]</span><span class="token comment"># ls</span>
ifcfg-ens33  <span class="token punctuation">..</span>.<span class="token punctuation">(</span>此处省略若干条<span class="token punctuation">)</span>
</code></pre>
<p>可以在这个目录下可以看到很多ifcfg开头的文件，这个目录就是存放网络配置的地方。<code>ifcfg-ens33</code>这个文件就是我们要配置的网卡的配置文件。</p>
<p>如果之前没有修改过网卡配置文件，默认情况下，原文件应该是这样的：</p>
<pre class=" language-bash"><code class="prism  language-bash">TYPE<span class="token operator">=</span>Ethernet
BOOTPROTO<span class="token operator">=</span>dhcp		<span class="token comment">#表示此网卡获取IP地址的方式</span>
DEFROUTE<span class="token operator">=</span>yes
IPV4_FAILURE_FATAL<span class="token operator">=</span>no
IPV6INIT<span class="token operator">=</span>yes
IPV6_AUTOCONF<span class="token operator">=</span>yes
IPV6_DEFROUTE<span class="token operator">=</span>yes
IPV6_FAILURE_FATAL<span class="token operator">=</span>no
IPV6_ADDR_GEN_MODE<span class="token operator">=</span>stable-privacy
NAME<span class="token operator">=</span>ens33			<span class="token comment">#此网卡的名称</span>
UUID<span class="token operator">=</span>4c0cc561-8aa7-4739-a796-641dae82614f	<span class="token comment">#此网卡的uid号</span>
DEVICE<span class="token operator">=</span>ens33	
ONBOOT<span class="token operator">=</span>no			<span class="token comment">#启动此网卡的时候使用这个配置文件</span>
IPV6_PEERDNS<span class="token operator">=</span>yes
IPV6_PEERROUTES<span class="token operator">=</span>yes
</code></pre>
<p><em>关于ONBOOT这一项，网上好多人说是为了开机加载。实际上，如果这一项为NO的话，无论怎么重启网络服务，你的ip都不会使用此配置文件的设置。所以，要想网卡使用这一份配置文件，请保持这一项为yes</em></p>
<h3 id="网络的配置有两种方式，可以直接修改这个配置文件，也可以使用网络管理工具nmcli">2.2网络的配置有两种方式，可以直接修改这个配置文件，也可以使用网络管理工具nmcli</h3>
<h4 id="我们先看修改配置文件的方式：">2.2.1我们先看修改配置文件的方式：</h4>
<p>直接用vim打开上述网卡配置文件：</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@localhost network-scripts<span class="token punctuation">]</span><span class="token comment">#vim ifcfg-ens33</span>
</code></pre>
<p>1）第一个需要修改的地方就是ONBOOT，原因上面已经说过，不再赘述。</p>
<pre class=" language-bash"><code class="prism  language-bash">ONBOOT<span class="token operator">=</span>yes
</code></pre>
<p>2）如果使用DHCP服务器自动分配的地址，那么就可以保存退出，去重启网络服务了。这里演示手动配置的ip。将以下内容写入上述配置文件尾部，注意用大写字母。</p>
<pre class=" language-bash"><code class="prism  language-bash">DNS1<span class="token operator">=</span>192.168.1.1		<span class="token comment">#dns地址</span>
IPADDR<span class="token operator">=</span>192.168.1.121	<span class="token comment">#IP地址</span>
NETMASK<span class="token operator">=</span>255.255.255.0 	<span class="token comment">#子网掩码(也可以写PREFIX=24 )</span>
GATEWAY<span class="token operator">=</span>192.168.1.1		<span class="token comment">#网关地址</span>
</code></pre>
<p>3）至此，网络的配置已经完毕。接下来就是从其网络服务了</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@localhost network-scripts<span class="token punctuation">]</span><span class="token comment"># systemctl restart network </span>
</code></pre>
<h3 id="使用nmcli命令行工具配置网络">2.2.2 使用nmcli命令行工具配置网络</h3>
<p>Redhat7之前的网络管理是通过ifcfg文件配置管理接口(device)，而现在是通过NetworkManager服务管理连接(connection)。</p>
<p>一个接口(device)可以有多个连接(connection)，但是同时只允许一个连接(connection)处于激活（active）状态。</p>
<p>简单理解就是，一个连接就是(connection)就是/etc/sysconfig/network-scripts/目录下的一个配置文件，接口(device)是物理设备，一个物理设置可以拥有多个配置文件，但只能有一个配置文件属于使用(active)状态；配置文件的生成与使用状态均由NetworkManager控制。</p>
<p>在使用nmcli命令行工具前，一定要保证NetworkManager服务为开启的状态，如果没有开启此服务，会出现以下错误：</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment">#尝试添加一个新的连接</span>
<span class="token punctuation">[</span>root@localhost network-scripts<span class="token punctuation">]</span><span class="token comment"># nmcli connection add type ethernet ifname ens33 con-name ens33 </span>
Error: NetworkManager is not running.
<span class="token comment">#开启此服务</span>
<span class="token punctuation">[</span>root@localhost network-scripts<span class="token punctuation">]</span><span class="token comment"># systemctl start NetworkManager</span>
<span class="token comment">#再次重试</span>
<span class="token punctuation">[</span>root@localhost network-scripts<span class="token punctuation">]</span><span class="token comment"># nmcli connection add type ethernet ifname ens33 con-name ens33 </span>
Connection <span class="token string">'ens33'</span> <span class="token punctuation">(</span>0a6e7ff9-1a31-4cbc-a606-c10954bababa<span class="token punctuation">)</span> successfully added.
</code></pre>
<p>创建连接</p>
<pre class=" language-bash"><code class="prism  language-bash">nmcli connection add <span class="token function">type</span> ethernet con-name 链接名 ifname 设备名
nmcli connection add <span class="token function">type</span> ethernet con-name 链接名 ifname 设备名 ip4 IP地址 gw4 网关地址
</code></pre>
<table><tbody><tr><td bgcolor="orange">注意：这里的设备名指的是网卡的名称，而连接名指的是配置文件的名称。可以不一致，为了便于对应，通常设为一致。</td></tr></tbody></table>
<p>修改连接</p>
<pre class=" language-bash"><code class="prism  language-bash">nmcli connection modify ens33 ipv4.addresses 192.168.80.88/24	<span class="token comment">#修改IP地址和子网掩码</span>
lnmcli connection modify eno1 ipv4.gateway 192.168.88.1		<span class="token comment">#修改网关</span>
lnmcli connection modify eno1 ipv4.dns 192.168.88.1			<span class="token comment">#修改dns地址</span>
lnmcli connection modify eno1 ipv4.method manual		<span class="token comment">#将获取ip的方式设置为手动</span>
lnmcli connection modify eno1 autoconnect <span class="token function">yes</span>		<span class="token comment">#让网卡使用此配置文件，也就是上面说的ONBOOT那个位置</span>
</code></pre>
<p>操作的说明</p>
<pre class=" language-bash"><code class="prism  language-bash">connection <span class="token comment">#对网络连接的操作</span>
modify		<span class="token comment">#修改操作</span>
ens33		<span class="token comment">#设备名称</span>
ipv4.addresses	<span class="token comment">#ipv4的地址（后面可以跟上子网掩码范围从1-32）</span>
ipv4.gateway	<span class="token comment">#网关地址</span>
ipv4.dns		<span class="token comment">#dns地址</span>
ipv4.method		<span class="token comment">#获取地址的方式，auto/manual</span>
autoconnect		<span class="token comment">#是否使用此配置，yes/no</span>
</code></pre>
<p>最后不要忘了重启网络服务。</p>
<p>以上，就是在Redhat7中手动配置静态ip的两种方式。</p>

