---


---

<h2 id="liunx中使用touch命令更改文件的时间">liunx中使用touch命令更改文件的时间</h2>
<ol>
<li>
<p>首先介绍下Linux系统中文件的三个时间</p>
<pre><code>1）modification time (mtime,修改时间)：这个时间指的是文件内容修改的时间，而不是文件属性的修改，当数据内容修改时，这个时间就会改变，用命令ls -l默认显示的就是这个时间。
    
2）status time （ctime,状态时间）：当一个文件的状态改变时，这个时间就会改变，例如更改了文件的权限与属性等，它就会改变。

3）access time （atime,访问时间）：当读取文件内容时，就会更改这个时间，例如使用cat 去读取/etc/man.config,那么该文件的atime就会改变。
</code></pre>
</li>
<li>
<p>touch的用法为：</p>
<pre><code> touch [-actmd] 文件

 参数：

 -a:仅修改access time

 -c:仅修改时间而不建立文件

 -t:后面可以接时间，格式为：[[CC]YY]MMDDhhmm [.SS]
		     *该参数修改的是访问时间和修改时间*

		CC - 年份的前两位 
		YY - 年份的后两位 
		MM - 月份 [01-12]
		DD - 日期 [01-31]
		hh - 时 [00-23]
		mm - 分 [00-59]
		SS - 秒 [00-61]


 -m:仅修改mtime

 -d:后面可以接日期，也可以使用--date="日期或时间"
</code></pre>
</li>
</ol>

