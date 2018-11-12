---


---

<p>此教程环境为：Redhat7.3系统，使用VMware虚拟机</p>
<h3 id="、第一步，加载iso镜像到vmware虚拟机上">1、第一步，加载iso镜像到VMware虚拟机上</h3>
<p>1）为虚拟机指定一个ISO磁盘镜像文件<br>
<img src="https://ws1.sinaimg.cn/large/006h9k0Tly1fwzqcg0kpoj30ye0pajtj.jpg" alt="" width="500" height="300"></p>
<p>2）然后，查看右下角的磁盘图标有没有亮，如果没有亮（亮的状态为右下角有绿色的小点），则右击磁盘图标----&gt;连接<br>
<img src="https://ws1.sinaimg.cn/large/006h9k0Tly1fwzqte5atbj308702k0so.jpg" alt=""></p>
<p>至此，镜像已经加载到虚拟机中了，相当于将光盘插入了光驱</p>
<h3 id="、将光盘挂在到系统中">2、将光盘挂在到系统中</h3>
<pre class=" language-bash"><code class="prism  language-bash">	*刚刚加载的光盘镜像在系统中被识别为/dev/sr0，若要使用此光盘中的文件，一定要挂在才可以。*

    <span class="token punctuation">[</span>root@www ~<span class="token punctuation">]</span><span class="token comment"># mount /dev/sr0 /mnt</span>
	mount: /dev/sr0 is write-protected, mounting read-only
</code></pre>
<h3 id="、编辑配置文件，yum仓库的配置文件">3、编辑配置文件，yum仓库的配置文件</h3>
<pre class=" language-bash"><code class="prism  language-bash">    <span class="token punctuation">[</span>root@www ~<span class="token punctuation">]</span><span class="token comment"># cd /etc/yum.repos.d/	#yum仓库配置文件所在的目录  </span>
</code></pre>
<p><strong>此目录在Redhat7中默认为空。然后创建一个新的文件，文件名随意，但一定要以 .repo 结尾</strong></p>
<pre class=" language-bash"><code class="prism  language-bash">    <span class="token punctuation">[</span>root@www yum.repos.d<span class="token punctuation">]</span><span class="token comment"># vim myrepo.repo  </span>
</code></pre>
<hr>
<p><strong>文件内容如下：</strong></p>
<pre class=" language-bash"><code class="prism  language-bash">    <span class="token punctuation">[</span>name<span class="token punctuation">]</span>		<span class="token comment">#仓库的名称</span>
    name<span class="token operator">=</span>myrepo		<span class="token comment">#此仓库的描述</span>
    baseurl<span class="token operator">=</span>file:///mnt		<span class="token comment">#安装包存放的地址，这里使用的本地源，所以指向本地路径，即光盘挂在的路径</span>
    gpgcheck<span class="token operator">=</span>0		<span class="token comment">#不校验安装包</span>
</code></pre>
<p>** 最后测试一下 **</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@www yum.repos.d<span class="token punctuation">]</span><span class="token comment"># yum clean all	 清除yum缓存  </span>
<span class="token punctuation">[</span>root@www yum.repos.d<span class="token punctuation">]</span><span class="token comment"># yum makecache 	缓存本地yum源  </span>
<span class="token punctuation">[</span>root@www yum.repos.d<span class="token punctuation">]</span><span class="token comment"># yum list 	测试</span>
</code></pre>

