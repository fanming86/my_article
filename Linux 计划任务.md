---


---

<ul>
<li>计划任务
<ul>
<li>
<ol>
<li>什么是计划任务</li>
</ol>
</li>
<li>
<ol start="2">
<li>为什么要使用计划任务</li>
</ol>
</li>
<li>
<ol start="3">
<li>一次性计划任务和永久性计划任务</li>
</ol>
</li>
</ul>
</li>
</ul>
<h3 id="、计划任务">1、计划任务</h3>
<p>举个简单的例子：<br>
闹钟，都用过把。例如在每天早上的七点半播放一段音乐来叫醒你。我们就可以把闹钟看成一个计划任务，在每天的七点半执行一个任务。这个任务是我们在事先设定好了的，让他在指定的时间执行某个任务，像这种为未来的某个时间制定一个任务的形式，我们就可以称之为计划任务。</p>
<h3 id="、为什么要使用计划任务">2、为什么要使用计划任务</h3>
<p>可以在指定的时间执行想要执行的任务，例如在深夜重启机器，而我们人又不想在深夜守着机器，这个时候就可以利用计划任务，在深夜某个时间点执行重启任务。</p>
<h3 id="、两种计划任务">3、两种计划任务</h3>
<p>计划任务分为两类，一类是一次性的，只能执行一次。还有一种是可以执行多次的，我们叫他永久性的计划任务。</p>
<h5 id="）一次性计划任务">1）一次性计划任务</h5>
<p><em><strong>一次性计划任务的服务名称</strong></em>：atd<br>
创建一次性计划任务前，要保证此服务是运行的状态：</p>
<pre><code>systemclt start atd			#启动服务
systemctl enable atd		#将此服务设置为开机启动
systemctl status atd		#查看此服务的状态，确定是启动的状态
</code></pre>
<p><em><strong>创建一次性计划任务，使用命令</strong></em> <code>at</code></p>
<pre><code>[root@www Desktop]# date	#查看当前时间
Mon Nov  5 16:48:44 CST 2018	
[root@www Desktop]# at 16:50	#创建一个计划任务在两分钟后
at&gt; echo hello world &gt; output.txt	#计划任务的内容，将hell world 写入output.txt文件
at&gt; &lt;EOT&gt;		#按ctrl+d结束输入
job 4 at Mon Nov  5 16:50:00 2018		#提示创建了一个计划任务

两分钟后。。。 

[root@www Desktop]# ll 
total 4
-rw-r--r--. 1 root root 12 Nov  5 16:50 output.txt
[root@www Desktop]# 
[root@www Desktop]# 
[root@www Desktop]# cat output.txt 
hello world
</code></pre>
<p>at命令的用法：<br>
<strong>-l 选项</strong>：	可以查看系统中所有用户的计划任务</p>
<pre><code>[root@www Desktop]# at -l
5	Mon Nov  5 17:00:00 2018 a root

5：任务号
Mon Nov  5 17:00:00 2018 ： 该任务执行的时间
root ： 执行改任务的身份
</code></pre>
<p><strong>-d  选项</strong>： 删除某个计划任务</p>
<pre><code>[root@www Desktop]# at -d 5		#删除任务号为5的任务
[root@www Desktop]# 
[root@www Desktop]# at -l		#在查看一次，可以发现刚才的计划任务已经没有了
[root@www Desktop]# 
</code></pre>
<h3 id="at-指定时间的方法有很多">at 指定时间的方法有很多</h3>
<hr>
<pre><code>&gt; 1）hh:mm小时:分钟(当天，如果时间已过，则在第二天执行)
&gt; 2）midnight(深夜),noon(中午),teatime(下午茶时间，下午4点),today,tomorrow等
&gt; 3）12小时计时制，时间后加am(上午)或pm(下午) 
&gt; 4）指定具体执行日期mm/dd/yy(月/日/年)或dd.mm.yy(日.月.年)
&gt; 5）相对计时法now + n units，now是现在时刻，n为数字，units是单位(minutes、hours、days、weeks)
</code></pre>
<p>例如：设置一个 明天下午2：20 的计划任务</p>
<pre><code>[root@www Desktop]# at 02:20pm tomorrow
at&gt; touch hello.py
at&gt; &lt;EOT&gt;
job 6 at Tue Nov  6 14:20:00 2018
</code></pre>
<p>可以使用 -f 选项或管道符来从文件中获取任务<br>
例如：</p>
<pre><code>#创建一个11月19日的计划任务
[root@www Desktop]# echo 'touch hello.txt'  &gt; test_at.txt
[root@www Desktop]# cat test_at.txt | at 18:20 11/19/18
job 7 at Mon Nov 19 18:20:00 2018

#创建一个两天后的计划任务
[root@www Desktop]# at -f test_at.txt 18:30 +2 days
job 8 at Wed Nov  7 18:30:00 2018
</code></pre>
<h5 id="）永久性计划任务">2）永久性计划任务</h5>

