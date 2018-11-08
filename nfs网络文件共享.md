---


---

<p>redhat7中使用nfs 来共享文件</p>
<p>此实验环境使用的是：<br>
Linux（客户端）-Linux（服务端）和<br>
windows（客户端）-Linux（服务端），<br>
以上Linux系统为Redhat7 ，windows为Windows10专业版</p>
<h2 id="、在做文件共享之前，需要做一下准备工作">**、在做文件共享之前，需要做一下准备工作</h2>
<ul>
<li>
<p>1）保证客户端机器和服务端机器网络的连通<br>
服务端使用的ip为  192.168.247.129<br>
Linux客户端ip为  192.168.247.130<br>
Windows的ip为  192.168.247.1</p>
</li>
<li>
<p>2）确保安装了nfs-utils  软件包</p>
<pre><code>  rpm -qa | grep nfs			#查看是否安装过nfs-utils
</code></pre>
</li>
<li>
<p>3）为了消除防火墙对服务的影响，这里直接关闭防火墙</p>
<pre><code>  systemctl stop firewalld
</code></pre>
</li>
</ul>
<h2 id="、linux（客户端）---linux（服务端）配置">1、Linux（客户端）—Linux（服务端）配置</h2>
<ul>
<li>
<h3 id="）服务端配置">1）服务端配置</h3>
<ul>
<li>
<p>a 、服务按装完成后，直接启动nfs服务：</p>
<pre><code>  systemctl start nfs
</code></pre>
</li>
<li>
<p>b、创建共享文件夹：</p>
<pre><code>  mkdir /public 
</code></pre>
</li>
<li>
<p>c、创建共享，编辑以下配置文件，此文件默认为空</p>
<pre><code>  vim /etc/exports		
</code></pre>
<p>此文件格式如下：</p>
<pre><code>  共享的目录名 （目录必须存在）	共享给哪些用户 （相应权限）
</code></pre>
<p>示例：</p>
<pre><code>  /public 	* (ro)
  /public1 	192.168.1.0/24(rw)
</code></pre>
</li>
<li>
<p>d、编辑好此文件后，保存退出。并重启nfs服务</p>
<pre><code>  systemctl restart nfs
</code></pre>
</li>
</ul>
<p><strong>至此，服务端配置完成</strong></p>
</li>
<li>
<h3 id="）linux客户端配置">2）Linux客户端配置</h3>
<ul>
<li>
<p>查看服务端共享的目录</p>
<pre><code>  showmount -e 192.168.247.129
</code></pre>
</li>
<li>
<p>挂载共享目录</p>
<pre><code>  mount -t nfs 192.168.247.129:/public  /public_mount
</code></pre>
</li>
<li></li>
</ul>
</li>
</ul>

