---


---

<ol>
<li>
<p>安装Python虚拟环境<br>
在centos中，系统已经集成了Python环境，直接可以使用，不用手动安装。</p>
<pre><code> 	1）pip install virtualenv             进行这一步要保证pip可以使用
 	若在此报错gcc，则需要安装python-devel
 	2）virtualenv   name    ——————创建虚拟环境
 	3）source name/activate ————激活虚拟环境
 	4）根据flask项目中需要的Python包安装
</code></pre>
</li>
<li>
<p>安装uwsgi</p>
<pre><code>1）pip install uwsgi
2）在项目的根目录创建文件名为config.ini文件，用于启动uwsgi
</code></pre>
<pre><code>[uwsgi]

socket = 127.0.0.1:5001     //启动程序时所使用的地址和端口，通常在本地运行flask项目，
                            //地址和端口是127.0.0.1:5000,
                            //不过在服务器上是通过uwsgi设置端口，通过uwsgi来启动项目，
                            //也就是说启动了uwsgi，也就启动了项目。
chdir = /root/pyenv/AjaxExample    //项目目录

wsgi-file = run.py      //flask程序的启动文件，通常在本地是通过运行  
                        //python run.py 来启动项目的
                        
callable = app      //程序内启用的application变量名

processes = 4     //处理器个数

threads = 2     //线程个数

stats = 127.0.0.1:9191      //获取uwsgi统计信息的服务地址 用于和nginx通信
</code></pre>
</li>
<li>
<p>安装nginx</p>
<pre><code>	*nginx的一些命令
	*启动： nginx
	*停止： nginx -s -stop
	*强制停止： pkill -9 nginx
	*查看nginx是否应用配置文件: nginx -t
	*查看版本： nginx -v
	
 1）nginx 可以使用yum直接安装
		 yum install yum-fastestmirror————安装源
		 yum install nginx
		 
 2）配置nginx
		 配置文件默认在/etc/nginx/nginx.conf
</code></pre>
</li>
</ol>
<pre><code>user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;     //错误日志
pid /run/nginx.pid;
include /usr/share/nginx/modules/*.conf;
events {
    worker_connections 1024;
}
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;
    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;
    server {
        listen       80 default_server;  //web服务端口
        listen       [::]:80 default_server;  
        server_name  39.106.56.137;  //公网ip
        root         /usr/share/nginx/html;
        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location / {
		include        uwsgi_params;
                uwsgi_pass     127.0.0.1:5001;    //需要和uwsgi的配置文件里socket项的地址
                                                  //相同,否则无法让uwsgi接收到请求。
                uwsgi_param UWSGI_PYHOME /root/pyenv;  //python的位置(虚拟环境下)
                uwsgi_param UWSGI_CHDIR /root/pyenv/AjaxExample;   //项目根目录
                uwsgi_param UWSGI_SCRIPT run:app;    //启动项目的主程序(在本地上运行
                                                     //这个主程序可以在flask内置的
                                                     //服务器上访问你的项目)
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}

</code></pre>
<p>至此，各项配置已经完成，不出意外的话，你就可以在浏览器中访问你的网站了</p>

