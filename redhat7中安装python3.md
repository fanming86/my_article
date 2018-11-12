---


---

<h2 id="redhat7安装python3">Redhat7安装python3</h2>
<ol>
<li>安装编译环境<br>
首先要保证yum仓库好使！！ ！<br>
关于yum仓库的配置，请移步我的<a href="http://www.fanming.shop/post/18/">另一篇博文</a></li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@host ~<span class="token punctuation">]</span><span class="token comment">#yum install gcc  </span>
</code></pre>
<ol start="2">
<li>下载源码包，并解压</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@host ~<span class="token punctuation">]</span><span class="token comment"># wget --no-check-certificate https://www.python.org/ftp/python/3.6.0/Python-3.6.0.tgz  </span>
<span class="token punctuation">[</span>root@host ~<span class="token punctuation">]</span><span class="token comment"># tar -xzvf Python-3.6.0.tgz  </span>
<span class="token punctuation">[</span>root@host ~<span class="token punctuation">]</span><span class="token comment"># cd Python-3.6.0  </span>
</code></pre>
<ol start="3">
<li>执行安装配置，指定安装路径</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@host Python-3.6.0<span class="token punctuation">]</span><span class="token comment"># ./configure --prefix=/usr/local/python3 </span>
</code></pre>
<ol start="5">
<li>build和安装</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@host Python-3.6.0<span class="token punctuation">]</span><span class="token comment"># make  </span>
<span class="token punctuation">[</span>root@host Python-3.6.0<span class="token punctuation">]</span><span class="token comment"># make install</span>
</code></pre>
<table><tbody><tr><td bgcolor="orange">在这里遇到一个错误，报错如下：<br>
zipimport.ZipImportError: can't decompress data; zlib not available   <br>
解决方法：
yum install zlib*  <br>
然后在次执行 make install	</td></tr></tbody></table>
<ol start="6">
<li>安装后建立一个链接，这样我们可以用python3直接运行程序，和python2区别开来。</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@host Python-3.6.0<span class="token punctuation">]</span><span class="token comment"># ln -s /usr/local/python3/bin/python3 /usr/bin/python3</span>
</code></pre>
<p><em><strong>特别提醒：建议不要将python3设置为系统中默认的python，设置后，yum仓库可能出问题。。。</strong></em></p>
<h2 id="安装pip">安装pip</h2>
<ol>
<li>下载并解压</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@host ~<span class="token punctuation">]</span><span class="token comment"># wget --no-check-certificate https://github.com/pypa/pip/archive/9.0.1.tar.gz  </span>
<span class="token punctuation">[</span>root@host ~<span class="token punctuation">]</span><span class="token comment"># tar -zvxf 9.0.1 -C pip-9.0.1    # 解压文件  </span>
<span class="token punctuation">[</span>root@host ~<span class="token punctuation">]</span><span class="token comment"># cd pip-9.0.1  </span>
</code></pre>
<ol start="2">
<li>使用 Python 3 安装</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@host pip-9.0.1<span class="token punctuation">]</span><span class="token comment"># python3 setup.py install </span>
</code></pre>
<ol start="3">
<li>将pip变成全局命令</li>
</ol>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@host ~<span class="token punctuation">]</span><span class="token comment"># ln -s /usr/local/python3/bin/pip /usr/bin/pip3</span>
接下来，你就可以使用pip3来安装python3的第三方库了。。。
</code></pre>

