---


---

<p>在互联网中传输数据的时候，需要使用IP地址。否则，数据包不知道会被发送到哪里去。</p>
<p>目前互联网中所用的是tcp/ip协议，其中IP为第四版的IPv4。不过，这是由32位组成，为了适应人们的习惯，已经将他转为十进制的数字了。</p>
<p>然而，人们对这种数字的记忆，实在不能记忆太多。但上互联网，又必须要使用ip，怎么办？？？</p>
<p>为了应付这个问题，有人提出意见，使用特定的文件，用来将主机名和ip做一个对应。如此一来，我们只用记住主机名就可以实现互联网的通讯了。这就是早期的 /etc/hosts文件。。</p>
<p>当后来计算机得到普及，上网的人更多了。这个文件就不能满足人们的需求了。这是伯克利大学发展出另一套阶层管理主机名ip对应的系统，bind</p>
<p>直到现在，我们都是在使用的这一套管理系统，这也是世界上目前使用最广泛的域名系统。</p>
<h2 id="关键词：">关键词：</h2>
<p>FQDN：(Fully Qualified Domain Name)全限定域名：同时带有主机名和域名的名称。（通过符号“.”）</p>
<p>例如：主机名是bigserver,<a href="http://xn--mycompany-fv8of05ajj7b.com">域名是mycompany.com</a>,<a href="http://xn--FQDNbigserver-2t1uz23nno7at23l.mycompany.com">那么FQDN就是bigserver.mycompany.com</a>。</p>
<p>（摘抄自百度百科  <a href="https://baike.baidu.com/item/FQDN">https://baike.baidu.com/item/FQDN</a>）</p>
<h2 id="dns查询有两种类型">DNS查询有两种类型</h2>
<pre><code>迭代查询

递归查询
</code></pre>
<p>关于迭代查询和递归查询方式的区别，这个段子表现的淋漓尽致，知乎的大神真是多啊，哈哈哈：</p>
<p>你想知道某个一起上法律课的女孩的电话，并且你偷偷拍了她的照片，回到寝室告诉一个很仗义的哥们儿，这个哥们儿二话没说，拍着胸脯告诉你，甭急，我替你查(此处完成了一次递归查询，即，问询者的角色更替)。然后他拿着照片问了学院大四学长，学长告诉他，这姑娘是xx系的；然后这哥们儿马不停蹄又问了xx系的办公室主任助理同学，助理同学说是xx系yy班的，然后很仗义的哥们儿去xx系yy班的班长那里取到了该女孩儿电话。(此处完成若干次迭代查询，即，问询者角色不变，但反复更替问询对象)最后，他把号码交到了你手里。完成整个查询过程。</p>
<p>作者：Tanswer<br>
链接：<a href="https://www.zhihu.com/question/53882349/answer/449572966">https://www.zhihu.com/question/53882349/answer/449572966</a><br>
来源：知乎</p>
<h2 id="dns协议的实现---bind">DNS协议的实现—bind</h2>
<pre><code>dns：协议
bind：dns协议的一种实现
named：bind程序运行的进程名

用户发起请求连接用的是UDP 53 端口
从服务器 刷新数据的时候用的 TCP 53 端口
</code></pre>
<h4 id="安装bind所需要的软件包：">安装bind所需要的软件包：</h4>
<p>bind-libs：被bind和bind-utils包中的程序共同用到的库文件；<br>
bind-utils：bind客户端程序集，例如dig, host, nslookup等；<br>
bind：提供的dns server程序、以及几个常用的测试程序；<br>
bind-chroot：选装，让named运行于jail模式下；</p>
<h4 id="启动named服务">启动named服务</h4>
<pre class=" language-bash"><code class="prism  language-bash">systemctl start named
systemctl <span class="token function">enable</span> named
</code></pre>
<h4 id="开放防火墙规则">开放防火墙规则</h4>
<pre class=" language-bash"><code class="prism  language-bash">firewall-cmd --add-service<span class="token operator">=</span>dns --permanent
firewall-cmd --reload
</code></pre>
<h2 id="配置文件">配置文件</h2>
<pre><code>/etc/named.conf ：主配置文件
/var/named/ ：数据库档案默认放置在这个目录
/etc/rfc1912.zones ：区域配置文件
</code></pre>
<h4 id="编辑配置：">编辑配置：</h4>
<pre class=" language-bash"><code class="prism  language-bash">vim  /etc/named.conf

options <span class="token punctuation">{</span>
	listen-on port 53  <span class="token punctuation">{</span> any<span class="token punctuation">;</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>  <span class="token comment">#可不设定，代表全部接受</span>
	directory  <span class="token string">"/var/named"</span><span class="token punctuation">;</span> <span class="token comment">#数据库默认放置的目录所在</span>
	dump-file  <span class="token string">"/var/named/data/cache_dump.db"</span><span class="token punctuation">;</span> <span class="token comment">#一些统计信息</span>
	statistics-file  <span class="token string">"/var/named/data/named_stats.txt"</span><span class="token punctuation">;</span>
	memstatistics-file <span class="token string">"/var/named/data/named_mem_stats.txt"</span><span class="token punctuation">;</span>
	allow-query  <span class="token punctuation">{</span> any<span class="token punctuation">;</span> <span class="token punctuation">}</span><span class="token punctuation">;</span>  <span class="token comment">#可不设定，代表全部接受</span>
	recursion <span class="token function">yes</span><span class="token punctuation">;</span>  <span class="token comment">#将自己视为客户端的一种查询模式</span>
	forward only<span class="token punctuation">;</span>  <span class="token comment">#可暂时不设定</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>  <span class="token comment">#最终记得要结尾符号！</span>
</code></pre>
<h4 id="编辑区域配置：">编辑区域配置：</h4>
<pre class=" language-bash"><code class="prism  language-bash">vim  /etc/rfc1912.zones

<span class="token comment">#添加一个正向解析的区域</span>
zone <span class="token string">"example.com"</span> IN <span class="token punctuation">{</span> // 这个 zone 的名称
	<span class="token function">type</span> master<span class="token punctuation">;</span> // 是什么类型
	<span class="token function">file</span> <span class="token string">"named.example.com"</span><span class="token punctuation">;</span>  // 正向解析的配置文件名，默认在/var/named/下
<span class="token punctuation">}</span><span class="token punctuation">;</span>

<span class="token comment">#添加一个逆向解析的区域</span>
zone <span class="token string">"100.168.192.in-addr.arpa"</span> IN <span class="token punctuation">{</span>
	<span class="token function">type</span> master<span class="token punctuation">;</span>
	<span class="token function">file</span> <span class="token string">"named.192.168.100"</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code></pre>
<h4 id="编辑正向解析的配置文件：">编辑正向解析的配置文件：</h4>
<p>#在/var/named目录下，复制一个文件做为样板：</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">cp</span> -p named.localhost named.example.com
vim named.example.com

<span class="token comment">#配置文件示例如下：</span>
<span class="token variable">$TTL</span> 1D
@  IN SOA example.com root.example.com. <span class="token punctuation">(</span>
							0  <span class="token punctuation">;</span> serial
							1D  <span class="token punctuation">;</span> refresh
							1H  <span class="token punctuation">;</span> retry
							1W  <span class="token punctuation">;</span> expire
							3H <span class="token punctuation">)</span>  <span class="token punctuation">;</span> minimum
@  IN  NS  ns.example.com. <span class="token comment"># DNS 服务器名称</span>
ns  IN  A  192.168.66.128 <span class="token comment"># DNS 服务器 IP</span>
fan  IN  A  192.168.66.129 <span class="token comment"># 相关的正向解析</span>
AAAA  ::1
</code></pre>
<h2 id="配置文件详解">配置文件详解</h2>
<p>序列号：serial，也即是数据库版本号；主服务器数据库内容发生变化时，其版本号递增；<br>
刷新时间间隔：refresh，从服务器每多久到从服务器检查序列号更新情况；<br>
重试时间间隔：retry，从服务器从主服务器请求同步解析失败时，再次发起请求尝试的时间间隔<br>
过期时长：expire，从服务器始终联系不到主服务器时，多久之后放弃主服务器同步；停止提供服务。</p>
<h5 id="资源记录类型">资源记录类型</h5>
<p>SOA：Start Of Authority，其实授权记录()额区域解析库有且只能有一个SOA记录，而且必须放在第一条<br>
NS： Name Service，区域名–&gt;FQDN 也成为名称服务器  （NS 和 A 记录要  一起出现）；一个区域解析库可以有多个NS记录；其中一个为主的<br>
A：  Address，地址记录，FQDN –&gt; IPV4<br>
AAAA：地址记录， FQDN –&gt; IPv6<br>
CNAME：Canonical Name，别名记录<br>
PTR：Pointer，反向指针记录：IP –&gt; FQDN<br>
MX： Mail eXchanger，邮件交换器<br>
优先级：0-99，数字越小优先级越高</p>
<h5 id="以下几点需要注意：">以下几点需要注意：</h5>
<p>(1) TTL可以从全局继承；<br>
(2) @表示当前区域的名称；<br>
(3) 相邻的两条记录其name相同时，后面的可省略；<br>
(4) 对于正向区域来说，各MX，NS等类型的记录的value为FQDN，此FQDN应该有一个A记录；</p>
<h2 id="测试">测试</h2>
<pre class=" language-bash"><code class="prism  language-bash">named-checkconf  /etc/named.conf  <span class="token comment">#可以经检查配置文件的语法错误</span>
systemctl restart named  <span class="token comment">#重启服务</span>
</code></pre>
<p>重启服务后，若发现服务无法正常启动，可以查看日志文件 /var/log/messages 进行排错</p>
<h4 id="客户端配置">客户端配置</h4>
<p>/etc/resolv.conf #在客户端配置DNS服务器地址</p>
<p>nslookup命令可用于正向解析的测试<br>
dig  可用于逆向解析的测试</p>

