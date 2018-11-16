---


---

<h2 id="前言">前言</h2>
<hr>
<blockquote>
<p>FTP 是File Transfer Protocol（文件传输协议）的英文简称。<br>
用于Internet上的控制文件的双向传输。<br>
同时，它也是一个应用程序（Application）。<br>
基于不同的操作系统有不同的FTP应用程序，而所有这些应用程序都遵守同一种协议以传输文件。</p>
</blockquote>
<hr>
<h3 id="vsftp提供了以下三种登陆方式">vsftp提供了以下三种登陆方式</h3>
<h4 id="匿名用户登陆anonymous">匿名用户登陆anonymous</h4>
<blockquote>
<p>使用anonymous是应用广泛的一种FTP服务器.如果用户在FTP服务器上没有帐号,那么用户可以以anonymous为用户名,以自己的电子邮件地址为密码进行登录.当匿名用户登录FTP服务器后,其登录目录为匿名FTP服务器的根目录/var/ftp.为了减轻FTP服务器的负载,一般情况下,应关闭匿名帐号的上传功能.</p>
</blockquote>
<h4 id="虚拟用户登陆guest">虚拟用户登陆guest</h4>
<blockquote>
<p>如果用户在FTP服务器上拥有帐号,但此帐号只能用于文件传输服务,那么该帐号就是guest,guest是真实帐号的一种形式,它们的不同之处在于,geust登录FTP服务器后,不能访问除宿主目录以外的内容.</p>
</blockquote>
<h4 id="本地用户登陆real">本地用户登陆real</h4>
<blockquote>
<p>real也称为本地帐号,就是以真实的用户名和密码进行登录,但前提条件是用户在FTP服务器上拥有自己的帐号.用真实帐号登录后,其登录的目录为用户自己的目录,该目录在系统建立帐号时系统就自动创建/etc/passwd文件中的用户</p>
</blockquote>
<hr>
<h3 id="、安装vsftpd">1、安装vsftpd</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment">#安装vsftpd</span>
<span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># yum install vsftpd -y</span>
<span class="token comment">#启动vsftpd</span>
<span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># systemctl start vsftpd</span>
<span class="token comment">#设置开机启动</span>
<span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># systemctl enable vsftpd</span>
<span class="token comment">#开放防火墙规则</span>
<span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># firewall-cmd --add-service=ftp --permanent</span>
<span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># firewall-cmd --reload</span>
</code></pre>
<h5 id="在本机进行测试，使用匿名用户登录测试">在本机进行测试，使用匿名用户登录测试</h5>
<p>此服务正常启动后，可以打开浏览器并在地址栏中输入：<a href="ftp://localhost">ftp://localhost</a>；此时应该可以看到共享的目录 pub ；默认情况下共享目录为/var/ftp。</p>
<h3 id="、配置文件中的公共配置">2、配置文件中的公共配置</h3>
<p>主配置文件：vim /etc/vsftpd/vsftpd.conf</p>
<pre class=" language-bash"><code class="prism  language-bash">write_enable<span class="token operator">=</span>YES	 	<span class="token comment">#是否允许用户具有在FTP服务器文件中执行写的权限,默认是no</span>
download_enable<span class="token operator">=</span>YES 	<span class="token comment">#设置用户是否有下载的权限，默认yes</span>
dirmessage_enable<span class="token operator">=</span>YES 	<span class="token comment">#激活目录信息,当远程用户更改目录时,将出现提示信息</span>
xferlog_enable<span class="token operator">=</span>YES 		<span class="token comment">#启用上传和下载日志功能</span>
xferlog_file<span class="token operator">=</span>/var/log/xferlog	<span class="token comment">#设置日志存放的位置</span>
listen_port<span class="token operator">=</span>4449	<span class="token comment">#修改服务端口</span>
</code></pre>
<h3 id="、针对匿名用户的配置">3、针对匿名用户的配置</h3>
<p>/var/ftp 目录作为匿名用户访问的目录</p>
<pre class=" language-bash"><code class="prism  language-bash">anonymous_enable<span class="token operator">=</span>YES 　　　　      <span class="token comment">#	#是否允许匿名访问  </span>
anon_upload_enable<span class="token operator">=</span>NO   　　　　   <span class="token comment">#上传  </span>
anon_mkdir_write_enable<span class="token operator">=</span>NO  　　  <span class="token comment">#创建  </span>
anon_other_write_enable<span class="token operator">=</span>NO        <span class="token comment">#删除  </span>
anon_root<span class="token operator">=</span>/var/ftp 　　　　　　　　 <span class="token comment">#匿名访问目录  </span>
local_root<span class="token operator">=</span>/home/vsftp            <span class="token comment">#本地用户访问目录  </span>
local_enable<span class="token operator">=</span>YES      　　　　    <span class="token comment"># 允许使用本地帐户进行FTP用户登录验证  </span>
</code></pre>
<h3 id="、针对本地用户的配置">4、针对本地用户的配置</h3>
<p>1）创建一个系统本地用户，并使其不能登录</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment">#创建用户 </span>
<span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># useradd -s /sbin/nologin ftpuser  </span>
<span class="token comment">#设置密码</span>
<span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># passwd ftpuser       </span>
<span class="token comment">#在根目录下创建目录public，用来共享</span>
<span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># mkdir /public       </span>
</code></pre>
<p>2）更改配置文件<br>
这里我们使用/public 目录作为本地用户访问的共享目录</p>
<pre class=" language-bash"><code class="prism  language-bash">local_enable<span class="token operator">=</span>YES      <span class="token comment">#是否允许使用本地帐户登录 </span>
local_root<span class="token operator">=</span>/public    <span class="token comment">#本地用户访问目录  </span>
local_umask<span class="token operator">=</span>022   　　<span class="token comment">#设置本地用户默认文件掩码022　　　</span>
chroot_local_user<span class="token operator">=</span>YES <span class="token comment">#可以将用户锁定在共享目录中　　　　</span>
</code></pre>
<h3 id="、重启ftp服务，便可以访问ftp服务器了">5、重启ftp服务，便可以访问ftp服务器了</h3>
<h5 id="使用本地用户登录-ftpuserpassword192.168.13.128-在浏览器中使用本地用户登录">使用本地用户登录 <a href="ftp://user:password@192.168.13.128">ftp://user:password@192.168.13.128</a> #在浏览器中使用本地用户登录</h5>
<p><img src="https://ws1.sinaimg.cn/large/006h9k0Tly1fwkmoued00j30g503uq37.jpg" alt=""></p>
<h5 id="使用匿名用户登录--ftp192.168.13.128-匿名用户访问，直接访问地址即可">使用匿名用户登录  <a href="ftp://192.168.13.128">ftp://192.168.13.128</a> #匿名用户访问，直接访问地址即可</h5>
<p><img src="https://ws1.sinaimg.cn/large/006h9k0Tly1fwkmqdxf3hj30g803zaac.jpg" alt=""></p>

