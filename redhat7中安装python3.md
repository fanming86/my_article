---


---

<h2 id="redhat7安装python3">Redhat7安装python3</h2>
<ol>
<li>
<p>安装编译环境<br>
首先要保证yum仓库好使！！ ！<br>
关于yum仓库的配置，请移步我的<a href="http://www.fanming.shop/post/18/">另一篇博文</a></p>
<pre><code> yum install gcc  
</code></pre>
</li>
<li>
<p>下载源码包，并解压</p>
<pre><code>  wget --no-check-certificate https://www.python.org/ftp/python/3.6.0/Python-3.6.0.tgz  
  tar -xzvf Python-3.6.0.tgz  
  cd Python-3.6.0  
</code></pre>
</li>
<li>
<p>执行安装配置，指定安装路径</p>
<pre><code> ./configure --prefix=/usr/local/python3 
</code></pre>
</li>
<li>
<p>build和安装</p>
<pre><code> make  
 make install
</code></pre>
 <table><tbody><tr><td bgcolor="orange">在这里遇到一个错误，报错如下：<br>
 zipimport.ZipImportError: can't decompress data; zlib not available   <br>
 解决方法：
 yum install zlib*  <br>
 然后在次执行 make install	</td></tr></tbody></table>
</li>
<li>
<p>安装后建立一个链接，这样我们可以用python3直接运行程序，和python2区别开来。</p>
<pre><code>  ln -s /usr/local/python3/bin/python3 /usr/bin/python3
</code></pre>
</li>
</ol>
<p><em><strong>特别提醒：建议不要将python3设置为系统中默认的python，设置后，yum仓库可能出问题。。。</strong></em></p>
<h2 id="安装pip">安装pip</h2>
<ol>
<li>
<p>下载并解压</p>
<pre><code> wget --no-check-certificate https://github.com/pypa/pip/archive/9.0.1.tar.gz  
 tar -zvxf 9.0.1 -C pip-9.0.1    # 解压文件  
 cd pip-9.0.1  
</code></pre>
</li>
<li>
<p>使用 Python 3 安装</p>
<pre><code> python3 setup.py install 
</code></pre>
</li>
</ol>

