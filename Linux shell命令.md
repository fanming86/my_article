---


---

<hr>
<p>date: 2016-12-20 15:50:23<br>
title: Linux bash shell 基础知识<br>
tags: Linux<br>
categories: linux</p>
<hr>
<p><strong>一、Shell基础知识</strong><br>
 Shell是一个命令行解释器，它为用户提供了一个面向Linux内核发送请求以便运行程序的界面系统级程序，用户可以用Shell来启动、挂起、停止甚至是编写一些程序；<br>
  Shell还是一个功能相当强大的编程语言，易编写，易调试，灵活性较强，Shell是解释执行的脚本语言，在Shell中可以直接调用Linux系统命令</p>
<p>echo [选项] [输出内容] # 输出命令 # -e 支持反斜线控制的字符转换<br>
第一个脚本</p>
<pre><code>#vi hello.sh
#!/bin/bash
#  The first program
echo -e "\e[1; 34m hello world. \e[0m"  #打印带颜色的内容   
</code></pre>
<p>执行脚本<br>
a. 赋予执行权限，直接运行</p>
<pre><code>chmod 755 hello.sh
./hello.sh
</code></pre>
<p>b. 通过Bash调用执行脚本</p>
<pre><code>bash hello.sh
</code></pre>
<p>二、Bash的基本功能</p>
<pre><code>命令别名与快捷键 
临时添加或删除别名 
i. alias #查看系统中所有所有的命令别名 
ii. alias 别名=‘原命令’ #设定命令别名 
iii. unalias 别名 #删除别名
别名永久生效 
i. vi ~/.bashrc #写入环境变量配置文件，重启后生效 
ii. source .bashrc #重新加载配置文件后，立即生效
常用快捷键 
i. ctrl+c 强制终止当前命令 
ii. ctrl+l 清屏 （等同于clear） 
iii. ctrl+a 光标移动到命令行首 
iv. ctrl+e 光标移动到命令行尾 
v. ctrl+u 从光标所在位置删除到行首 
vi. ctrl+z 把命令放入后台 
vii. ctrl+r 在历史命令中搜索
历史命令：history [选项] [历史命令保存文件] 
-c 清空历史命令 
-w 把缓存中的历史命令写入历史命令保存文件 ~/.bash_history 
历史命令的调用： 
● 使用上、下箭头调用以前的历史命令 
● 使用“!n”重复执行第n条历史命令 
● 使用”!!”重复执行上一条命令 
● 使用”!字串”重复执行最后一条以该字串开头的命令
输出重定向

标准输出重定向： 
i. 命令 &gt; 文件 #以覆盖的方式，把命令的正确输出输出到指定的文件或设备当中 
ii. 命令 &gt;&gt; 文件 #以追加的方式，把命令的正确输出输出到指定的文件或设备当中
标准错误输出重定向： 
i. 错误命令 2&gt;文件 #以覆盖的方式，把命令的错误输出输出到指定的文件或设备当中 
ii. 错误命令 2 &gt;&gt;文件 #以追加的方式，把命令的错误输出输出到指定的文件或设备当中
正确输出和错误输出同时保存 
i. 命令 &gt; 文件 2 &gt;&amp;1 #以覆盖的方式，把正确输出和错误输出都保存在同一个文件当中 
ii. 命令 &gt;&gt; 文件 2 &gt;&amp;1 #以追加的方式，把正确输出和错误输出都保存在同一个文件当中 
iii. 命令 &amp;&gt;文件 #以覆盖的方式，把正确输出和错误输出都保存在同一个文件当中 
iv. 命令 &amp;&gt;&gt;文件 #以追加的方式，把正确输出和错误输出都保存在同一个文件当中 
v. 命令 &gt;&gt;文件1 2 &gt;&gt;文件2 #以追加的方式，把正确输出追加到文件1中，把错误的输出追加到文件2中
输入重定向：wc [选项] [文件名] 
-c 统计字节数 
-w 统计单词数 
-l 统计行数 
wc 直接输入内容，ctrl+d退出
</code></pre>
<p>多命令执行</p>
<pre><code>a. ； 命令1;命令2 #多命令顺序执行，命令之间没有任何逻辑联系 
b. &amp;&amp; 命令1&amp;&amp;命令2 #逻辑与，只有当命令1正确执行，命令2才会执行 
c. || 命令1||命令2 #逻辑或，只有当命令1执行不正确，命令2才会执行 
d. 管道符： 命令1 | 命令2 #命令1的正确输出作为命令2的操作对象，例： 
i. ls -lh /etc | more 分屏显示ls查询的结果 
ii. netstat -an | grep ESTABLISHED | wc -l #查询有多少个网络与服务器建立了连接
通配符 
a. ？ 匹配一个任意字符 
b. * 匹配0个或任意多个任意字符，也就是可以匹配任何内容 
c. [] 匹配中括号中任意一个字符。例如：[abc]代表一定匹配一个字符，或者是a,或者是b，或者是c。 
d. [-] 匹配括号中任意一个字符，-代表一个范围。例如：[a-z]代表匹配一个小写字母 
e. [^] 逻辑非，表示匹配不是中括号内的一个字符。例如：[^0-9]代表匹配一个不是数字的字符
其它特殊符号 
a. ‘ 单引号。在单引号中所有的特殊符号，如“$” 和”`”（反引号）都没有特殊含义 
b. “” 双引号。在双引号中特殊符号都没有特殊含义，但是“$”、”`”、和”\”是例外，拥有”调用变量的值“、”引用命令“和”转义符“的特殊含义。 
c. “ 反引号。反引号括起来的内容是系统命令，在Bash中会先执行它。和$()作用一样，不过推荐使用$(),因为反引号非常容易看错 
d. $() 和反引号的作用一样，用来引用系统命令 
e. # 在Shell脚本中，#开头的行代表注释 
f. $ 用于调用变量的值，如需要调用变量name的值时，需要用$name的方式得到变量的值 
g. \ 转义符，跟在\之后的特殊符号将失去特殊含义，变为普通字符。如$将输出”$“符号，而不是当作是变量引用。
</code></pre>

