---


---

<p>redhat7中使用nfs 来共享文件</p>
<p>此实验环境使用的是：<br>
Linux（客户端）-Linux（服务端）<br>
以上Linux系统为Redhat7</p>
<h2 id="、在做文件共享之前，需要做一下准备工作">**、在做文件共享之前，需要做一下准备工作</h2>
<ul>
<li>
<p>1）保证客户端机器和服务端机器网络的连通<br>
服务端使用的ip为  192.168.247.128<br>
Linux客户端ip为  192.168.247.130</p>
</li>
<li>
<p>2）确保安装了nfs-utils  软件包，（两台机器）</p>
<pre><code>  rpm -qa | grep nfs			#查看是否安装过nfs-utils
</code></pre>
<p>若没有安装，使用yum即可安装：<a href="http://www.fanming.shop/post/18/">yum仓库配置方法</a></p>
<pre><code>  yum install nfs-utils
</code></pre>
</li>
<li>
<p>3）为了消除防火墙对服务的影响，这里直接关闭防火墙  （两台机器）</p>
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
</code></pre>
<blockquote>
<p>括号中的部分选项<br>
ro	 只读<br>
rw 	只写<br>
root_squash 	当NFS客户端以root管理员身份访问时，映射为NFS服务器的匿名用户。<br>
no_root_squash 	当NFS客户端以root管理员身份访问时，映射为NFS服务器root管理员。<br>
all_squash 	无论NFS客户端使用什么账号访问，均映射为NFS服务器的匿名用户。<br>
sync 	同时将数据写入到内存与硬盘当中，保证数据不丢失。<br>
async 	先将数据保存到内存，然后再写入磁盘；这样效率更加高，但是可能会丢失数据。</p>
</blockquote>
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
<pre><code>  showmount -e 192.168.247.128
  
  执行结果如下：
  [root@example-com /]# showmount -e 192.168.247.128
  Export list for 192.168.247.128:
  /public *
</code></pre>
</li>
<li>
<p>挂载共享目录</p>
<pre><code>  #先创建挂载点
  mkdir /public_mount
  
  #将服务器共享的目录挂载在本地的/public_mount目录下
  mount -t nfs 192.168.247.128:/public /public_mount/
</code></pre>
</li>
<li>
<p>测试挂载<br>
使用df命令查看挂载情况：</p>
<pre><code> [root@example-com /]# df -TH
 文件系统                类型      容量  已用  可用 已用% 挂载点
 ........
 192.168.247.128:/public nfs4       19G  3.5G   15G   19% /public_mount
</code></pre>
<p>可以看到public目录已经挂载成功！</p>
<p><em><strong>尝试在挂载的目录创建文件</strong></em></p>
<pre><code> [root@example-com public_mount]# touch hello.py
 touch: 无法创建"hello.py": 只读文件系统
</code></pre>
<p>提示只读文件系统！！！<br>
因为我们在服务器共享的时候，给的权限为 ro  。（应该记得我说的是哪里把，不记得的话就往上看看）<br>
解决方法：<br>
将共享文件时的权限改为rw</p>
<pre><code> root@www /]# vim /etc/exports

 /public         *(rw)

 [root@www /]# exportfs -r 		#刷新exports文件
</code></pre>
<p><em><strong>改完后，再次尝试创建文件</strong></em></p>
<pre><code> [root@example-com public_mount]# touch hello.py
 touch: 无法创建"hello.py": 权限不够
</code></pre>
<p>依然报错，但是错误与上次不同。！！！<br>
导致这个错误的原因是共享的文件夹对其他人没有写权限。<br>
查看服务端共享文件夹的权限：</p>
<pre><code> [root@www /]# ll -d /public/
 drwxr-xr-x. 2 root root 6 11月  8 18:43 /public/
</code></pre>
<p>解决方法：</p>
<pre><code> [root@www /]# chmod o+w /public/
 #再次查看权限
 [root@www /]# ll -d /public/
 drwxr-xrwx. 2 root root 6 11月  8 18:43 /public/
</code></pre>
<p><strong>再一次创建文件：</strong></p>
<pre><code> [root@example-com public_mount]# touch hello.py
 [root@example-com public_mount]# ll
 总用量 0
 -rw-r--r--. 1 nfsnobody nfsnobody 0 11月  8 19:04 hello.py
 [root@example-com public_mount]# 
</code></pre>
<p>创建成功！！！<br>
至此，即可以在客户端操作共享文件夹里面的内容了</p>
</li>
</ul>
</li>
</ul>

