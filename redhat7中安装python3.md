---


---

<h2 id="redhat7安装python3">Redhat7安装python3</h2>
<pre><code>1. 安装编译环境
	a. yum install gcc 
2. 下载源码包，并解压
	a. wget --no-check-certificate https://www.python.org/ftp/python/3.6.0/Python-3.6.0.tgz
	b. tar -xzvf Python-3.6.0.tgz
	c. cd Python-3.6.0
3. 执行安装配置，指定安装路径
	a. ./configure --prefix=/usr/local/python3
4. build和安装
	a. make &amp;&amp; make install
5. 安装后建立一个链接，这样我们可以用python3直接运行程序，和python2区别开来。
	a. ln -s /usr/local/python3/bin/python3 /usr/bin/python3
</code></pre>
<h3 id="安装pip">安装pip</h3>
<pre><code>1. 下载并解压
	a. wget --no-check-certificate https://github.com/pypa/pip/archive/9.0.1.tar.gz
	b. tar -zvxf 9.0.1 -C pip-9.0.1    # 解压文件
	c. cd pip-9.0.1
2. 使用 Python 3 安装
	a. python3 setup.py install
</code></pre>

