---


---

<h2 id="客户端配置">客户端配置</h2>
<h3 id="客户端安装-iscsi-initiator-utils">1. 客户端安装 iscsi-initiator-utils</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@client ~<span class="token punctuation">]</span><span class="token comment"># yum install iscsi-initiator-utils -y</span>
</code></pre>
<h3 id="编辑文件">2. 编辑文件</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@client ~<span class="token punctuation">]</span><span class="token comment"># vim /etc/iscsi/initiatorname.iscsi </span>
<span class="token comment">#将iqn改为服务端允许连接的iqn</span>
InitiatorName<span class="token operator">=</span>iqn.2018-11.com.example:target
</code></pre>
<h3 id="客户端发现">3. 客户端发现</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@client ~<span class="token punctuation">]</span><span class="token comment"># iscsiadm --mode discoverydb --type sendtargets --portal 192.168.0.252 --discover</span>
192.168.0.252:3260,1 iqn.2018-11.com.example:target
<span class="token comment">#发现服务端的target</span>
</code></pre>
<h3 id="客户端登录">4. 客户端登录</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@client ~<span class="token punctuation">]</span><span class="token comment"># iscsiadm --mode node --targetname iqn.2018-11.com.example:target --portal 192.168.0.252:3260 --login</span>
Logging <span class="token keyword">in</span> to <span class="token punctuation">[</span>iface: default, target: iqn.2018-11.com.example:target, portal: 192.168.0.252,3260<span class="token punctuation">]</span> <span class="token punctuation">(</span>multiple<span class="token punctuation">)</span>
Login to <span class="token punctuation">[</span>iface: default, target: iqn.2018-11.com.example:target, portal: 192.168.0.252,3260<span class="token punctuation">]</span> successful.
<span class="token comment"># 看到最后的 successful  那就是登录成功了！！！</span>
</code></pre>
<p>登录后，就可以在自己电脑本地看到多了一块磁盘。fdisk -l  可以查看本地磁盘和分区。可以像操作本地磁盘一样，可以进行分区，可以格式化，也可以挂载！！！</p>
<h3 id="查看本地磁盘">查看本地磁盘</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@client ~<span class="token punctuation">]</span><span class="token comment"># fdisk -l	</span>
（。。。省略n行）
磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
Units <span class="token operator">=</span> 扇区 of 1 * 512 <span class="token operator">=</span> 512 bytes
扇区大小<span class="token punctuation">(</span>逻辑/物理<span class="token punctuation">)</span>：512 字节 / 512 字节
I/O 大小<span class="token punctuation">(</span>最小/最佳<span class="token punctuation">)</span>：512 字节 / 4194304 字节

<span class="token comment">#我这里的sdb 就是iSCSI共享设备</span>
</code></pre>
<h3 id="分区">分区</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># fdisk /dev/sdb</span>
<span class="token comment">#我这里分了两个区，分区的步骤省略掉了，分完区，不要忘了刷新分区表！！！</span>
<span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># partprobe </span>
<span class="token punctuation">[</span>root@client /<span class="token punctuation">]</span><span class="token comment"># fdisk -l /dev/sdb		#查看sdb的分区情况</span>

磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
Units <span class="token operator">=</span> 扇区 of 1 * 512 <span class="token operator">=</span> 512 bytes
扇区大小<span class="token punctuation">(</span>逻辑/物理<span class="token punctuation">)</span>：512 字节 / 512 字节
I/O 大小<span class="token punctuation">(</span>最小/最佳<span class="token punctuation">)</span>：512 字节 / 4194304 字节
磁盘标签类型：dos
磁盘标识符：0x6dbe5335

   设备 Boot      Start         End      Blocks   Id  System
/dev/sdb1            8192    10493951     5242880   83  Linux
/dev/sdb2        10493952    14688255     2097152   83  Linux

</code></pre>
<h3 id="格式化">格式化</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@client pub<span class="token punctuation">]</span><span class="token comment"># mkfs.xfs /dev/sdb1</span>
meta-data<span class="token operator">=</span>/dev/sdb1              isize<span class="token operator">=</span>512    agcount<span class="token operator">=</span>4, agsize<span class="token operator">=</span>327680 blks
         <span class="token operator">=</span>                       sectsz<span class="token operator">=</span>512   attr<span class="token operator">=</span>2, projid32bit<span class="token operator">=</span>1
         <span class="token operator">=</span>                       crc<span class="token operator">=</span>1        finobt<span class="token operator">=</span>0, sparse<span class="token operator">=</span>0
data     <span class="token operator">=</span>                       bsize<span class="token operator">=</span>4096   blocks<span class="token operator">=</span>1310720, imaxpct<span class="token operator">=</span>25
         <span class="token operator">=</span>                       sunit<span class="token operator">=</span>0      swidth<span class="token operator">=</span>0 blks
naming   <span class="token operator">=</span>version 2              bsize<span class="token operator">=</span>4096   ascii-ci<span class="token operator">=</span>0 ftype<span class="token operator">=</span>1
log      <span class="token operator">=</span>internal log           bsize<span class="token operator">=</span>4096   blocks<span class="token operator">=</span>2560, version<span class="token operator">=</span>2
         <span class="token operator">=</span>                       sectsz<span class="token operator">=</span>512   sunit<span class="token operator">=</span>0 blks, lazy-count<span class="token operator">=</span>1
realtime <span class="token operator">=</span>none                   extsz<span class="token operator">=</span>4096   blocks<span class="token operator">=</span>0, rtextents<span class="token operator">=</span>0
</code></pre>
<h3 id="挂载">挂载</h3>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token punctuation">[</span>root@client pub<span class="token punctuation">]</span><span class="token comment"># mkdir /pub/disk1 -p		#创建挂载点</span>
<span class="token punctuation">[</span>root@client pub<span class="token punctuation">]</span><span class="token comment"># mount /dev/sdb1 /pub/disk1	#挂载</span>
</code></pre>

