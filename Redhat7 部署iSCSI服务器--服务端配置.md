---


---

<h2 id="前言">前言</h2>
<blockquote>
<p>取得外部磁盘容量的做法，主要所有 NAS 和 SAN 两大类的方式。<br>
NAS 可以想成时一台已经定制化的服务器，主要提供 nfs 、smb 等网络文件系统<br>
SAN 则是一种外接式存储设备，可以通过 san 取得外部的磁盘设备（非文件系统）<br>
SAN 早期使用光纤通道，由于以太网络的发展，近来使用iSCSI协议在 tcp/ip 架构上工作<br>
iSCSI 协议主要分为iSCSI target （提供磁盘设备）和iSCSI initiator （访问target 磁盘）<br>
以上内容，均摘抄自《鸟哥的Linux私房菜–服务器架设篇（第三版）》</p>
</blockquote>
<p>此实验我们将使用两个虚拟机，分别作为服务器和客户端。</p>
<blockquote>
<p>服务端：rhel7  ip：192.168.0.252<br>
客户端：rhel7  ip：192.168.0.253</p>
</blockquote>
<h2 id="服务端配置">服务端配置</h2>
<h3 id="安装iscsi-target服务">1. 安装iscsi target服务</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@server ~<span class="token punctuation">]</span><span class="token comment"># yum install -y targetcli</span>
</code></pre>
<h3 id="设置为开机启动">2. 设置为开机启动</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@server ~<span class="token punctuation">]</span><span class="token comment"># systemctl enable target</span>
</code></pre>
<h3 id="配置-iscsi-target-，大多数目标配置是通过targetcli命令以交互方式完成的。">3. 配置 iSCSI target ，大多数目标配置是通过targetcli命令以交互方式完成的。</h3>
<h4 id="进入target命令行工具">3.1 进入target命令行工具</h4>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@server ~<span class="token punctuation">]</span><span class="token comment"># targetcli </span>
Warning: Could not load preferences <span class="token function">file</span> /root/.targetcli/prefs.bin.
targetcli shell version 2.1.fb41
Copyright 2011-2013 by Datera, Inc and others.
For <span class="token function">help</span> on commands, <span class="token function">type</span> <span class="token string">'help'</span><span class="token keyword">.</span>

/<span class="token operator">&gt;</span> <span class="token function">ls</span>
o- / <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">[</span><span class="token punctuation">..</span>.<span class="token punctuation">]</span>
  o- backstores <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span>.<span class="token punctuation">[</span><span class="token punctuation">..</span>.<span class="token punctuation">]</span>
  <span class="token operator">|</span> o- block <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span>.<span class="token punctuation">[</span>Storage Objects: 0<span class="token punctuation">]</span>
  <span class="token operator">|</span> o- fileio <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">[</span>Storage Objects: 0<span class="token punctuation">]</span>
  <span class="token operator">|</span> o- pscsi <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>Storage Objects: 0<span class="token punctuation">]</span>
  <span class="token operator">|</span> o- ramdisk <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>Storage Objects: 0<span class="token punctuation">]</span>
  o- iscsi <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>Targets: 0<span class="token punctuation">]</span>
  o- loopback <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span>. <span class="token punctuation">[</span>Targets: 0<span class="token punctuation">]</span>
/<span class="token operator">&gt;</span> 
</code></pre>
<h4 id="创建共享设备块，可以使用-devsdb-之类的块设备或以前创建的逻辑卷">3.2 创建共享设备块，可以使用 /dev/sdb 之类的块设备或以前创建的逻辑卷</h4>
<pre class=" language-bash"><code class="prism  language-bash">/<span class="token operator">&gt;</span> <span class="token function">cd</span> backstores/block 
/backstores/block<span class="token operator">&gt;</span> <span class="token function">ls</span>
o- block <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span>. <span class="token punctuation">[</span>Storage Objects: 0<span class="token punctuation">]</span> 
/backstores/block<span class="token operator">&gt;</span> create share_disk /dev/sdb
Created block storage object share_disk using /dev/sdb.
/backstores/block<span class="token operator">&gt;</span> 
</code></pre>
<h4 id="创建iqn，使用名为-target-的目标创建名为-iqn.2018-11.com.example-的-iqn-iscsi-qualified-name，并获取关联的-tpg-target-portal-group">3.3 创建iqn，使用名为 target 的目标创建名为 iqn.2018-11.com.example 的 IQN (Iscsi Qualified Name)，并获取关联的 TPG (Target Portal Group)</h4>
<blockquote>
<p>从Red Hat Enterprise Linux 7.1开始，无论何时创建目标，都会创建默认 portal。</p>
</blockquote>
<p><strong>IQN (Iscsi Qualified Name)</strong>：iSCSI有一套自己共享target文件名的定义，基本上通过iSCSI共享的文件名都是以 iqn 开头，通常情况下 iqn 的写法为：</p>
<blockquote>
<p>iqn.yyyy-mm.&lt; reversed domain name &gt;:identifier<br>
iqn.年份-月	  .  域名的反写						:共享的 target 名称</p>
</blockquote>
<pre class=" language-bash"><code class="prism  language-bash">/<span class="token operator">&gt;</span> <span class="token function">cd</span> /iscsi 
/iscsi<span class="token operator">&gt;</span> <span class="token function">ls</span>
o- iscsi <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>Targets: 0<span class="token punctuation">]</span>
</code></pre>
<hr>
<pre class=" language-bash"><code class="prism  language-bash">/iscsi<span class="token operator">&gt;</span> create iqn.2018-11.com.example:target
Created target iqn.2018-11.com.example:target.
Created TPG 1.
Global pref auto_add_default_portal<span class="token operator">=</span>true
Created default portal listening on all IPs <span class="token punctuation">(</span>0.0.0.0<span class="token punctuation">)</span>, port 3260.
/iscsi<span class="token operator">&gt;</span> 
</code></pre>
<hr>
<pre class=" language-bash"><code class="prism  language-bash">/iscsi<span class="token operator">&gt;</span> <span class="token function">ls</span>
o- iscsi <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>Targets: 1<span class="token punctuation">]</span>
<span class="token operator">|</span> o- iqn.2018-11.com.example:target <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>TPGs: 1<span class="token punctuation">]</span>
<span class="token operator">|</span>   o- tpg1 <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span>. <span class="token punctuation">[</span>no-gen-acls, no-auth<span class="token punctuation">]</span>
<span class="token operator">|</span>     o- acls <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>ACLs: 0<span class="token punctuation">]</span>
<span class="token operator">|</span>     o- luns <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>LUNs: 0<span class="token punctuation">]</span>
<span class="token operator">|</span>     o- portals <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>Portals: 1<span class="token punctuation">]</span>
<span class="token operator">|</span>       o- 0.0.0.0:3260 <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span>. <span class="token punctuation">[</span>OK<span class="token punctuation">]</span>
</code></pre>
<ul>
<li>在tpg1下面，定义了三个对象：
<ul>
<li>acls（访问控制列表：限制对资源的访问），</li>
<li>luns（逻辑单元号：定义导出的资源），</li>
<li>portals（定义到达导出资源的方式;包含成对的IP地址和端口）。</li>
</ul>
</li>
</ul>
<h4 id="创建lun">3.4  创建LUN</h4>
<p><strong>LUN （ Logical Unit Number ）:</strong>  共享设备块的编号（逻辑单位区号）</p>
<p>在该iqn下创建lun0，这样就跟上文创建的磁盘文件 share_disk  关联起来</p>
<pre class=" language-bash"><code class="prism  language-bash">/<span class="token operator">&gt;</span> <span class="token function">cd</span> /iscsi/iqn.2018-11.com.example:target/tpg1/luns 
/iscsi/iqn.20<span class="token punctuation">..</span>.get/tpg1/luns<span class="token operator">&gt;</span> <span class="token function">ls</span>
o- luns <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>LUNs: 0<span class="token punctuation">]</span>
/iscsi/iqn.20<span class="token punctuation">..</span>.get/tpg1/luns<span class="token operator">&gt;</span> 
/iscsi/iqn.20<span class="token punctuation">..</span>.get/tpg1/luns<span class="token operator">&gt;</span> create /backstores/block/share_disk 
Created LUN 0.
</code></pre>
<h4 id="创建acl">3.5  创建ACL</h4>
<p>然后在该iqn下创建acls访问控制条目，允许客户端的iqn访问该磁盘文件</p>
<pre class=" language-bash"><code class="prism  language-bash">/<span class="token operator">&gt;</span> <span class="token function">cd</span> /iscsi/iqn.2018-11.com.example:target/tpg1/acls 
/iscsi/iqn.20<span class="token punctuation">..</span>.get/tpg1/acls<span class="token operator">&gt;</span> 
/iscsi/iqn.20<span class="token punctuation">..</span>.get/tpg1/acls<span class="token operator">&gt;</span> create iqn.2018-11.com.example:target
Created Node ACL <span class="token keyword">for</span> iqn.2018-11.com.example:target
Created mapped LUN 0.
</code></pre>
<h4 id="创建portal">3.6  创建PORTAL</h4>
<p>最后在该iqn下创建门户IP</p>
<pre class=" language-bash"><code class="prism  language-bash">/iscsi/iqn.20<span class="token punctuation">..</span>.get/tpg1/acls<span class="token operator">&gt;</span> <span class="token function">cd</span> <span class="token punctuation">..</span>/portals/
/iscsi/iqn.20<span class="token punctuation">..</span>./tpg1/portals<span class="token operator">&gt;</span> <span class="token function">ls</span>
o- portals <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span>. <span class="token punctuation">[</span>Portals: 1<span class="token punctuation">]</span>
  o- 0.0.0.0:3260 <span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span> <span class="token punctuation">[</span>OK<span class="token punctuation">]</span>
/iscsi/iqn.20<span class="token punctuation">..</span>./tpg1/portals<span class="token operator">&gt;</span> create 192.168.0.252 3260	<span class="token comment">#直接创建时，报错</span>
Using default IP port 3260
Could not create NetworkPortal <span class="token keyword">in</span> configFS
/iscsi/iqn.20<span class="token punctuation">..</span>./tpg1/portals<span class="token operator">&gt;</span> 
/iscsi/iqn.20<span class="token punctuation">..</span>./tpg1/portals<span class="token operator">&gt;</span> delete 0.0.0.0 3260	<span class="token comment">#先将原来的删除掉</span>
Deleted network portal 0.0.0.0:3260
/iscsi/iqn.20<span class="token punctuation">..</span>./tpg1/portals<span class="token operator">&gt;</span> 
/iscsi/iqn.20<span class="token punctuation">..</span>./tpg1/portals<span class="token operator">&gt;</span> create 192.168.0.252 3260	<span class="token comment">#在创建，成功</span>
Using default IP port 3260
Created network portal 192.168.0.252:3260.
/iscsi/iqn.20<span class="token punctuation">..</span>./tpg1/portals<span class="token operator">&gt;</span> 
</code></pre>
<h4 id="配置完成，退出命令行工具">3.7 配置完成，退出命令行工具</h4>
<pre class=" language-bash"><code class="prism  language-bash">/iscsi/iqn.20<span class="token punctuation">..</span>./tpg1/portals<span class="token operator">&gt;</span> <span class="token keyword">exit</span>
Global pref auto_save_on_exit<span class="token operator">=</span>true
Last 10 configs saved <span class="token keyword">in</span> /etc/target/backup.
Configuration saved to /etc/target/saveconfig.json
</code></pre>
<h4 id="配置防火墙，开放target服务">3.8 配置防火墙，开放target服务</h4>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@server ~<span class="token punctuation">]</span><span class="token comment"># firewall-cmd --add-service=iscsi-target --permanent</span>
<span class="token punctuation">[</span>root@server ~<span class="token punctuation">]</span><span class="token comment"># firewall-cmd --reload</span>
</code></pre>
<h4 id="检查端口是否被监听">3.9 检查端口是否被监听</h4>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@server ~<span class="token punctuation">]</span><span class="token comment"># netstat -lantup | grep 3260</span>
tcp        0      0 192.168.0.252:3260      0.0.0.0:*               LISTEN      -  
</code></pre>

