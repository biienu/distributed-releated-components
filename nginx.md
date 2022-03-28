@[toc](文章结构)

nginx 下载地址:<(http://nginx.org/en/download.html)>

#  ngingx是什么?



## 1.1 简介

Nginx （engine x） 是一个高性能的Web服务器和反向代理服务器，也可以作为邮件代理服务器。

Nginx 特点是占有内存少，并发处理能力强，以高性能、低系统资源消耗而闻名，Nginx官方测试为5万并发请求。与Nginx同类型的Web服务器还有Apache、Lighttpd（音同lighty）、Tengine（阿里巴巴的） 等。Nginx 的并发处理能力在同类型的Web服务器中表现极好（Apache、Lighttpd），在全世界范围内大量的网站使用了Nginx，国内互联网中也大量使用了Nginx，比如：淘宝、新浪、搜狐、网易、美团等。

Nginx是免费开源的，同时Nginx也有收费的商业版本，商业版本提供了性能优化、宕机等紧急问题处理等技术支持和服务。

## 1.2 反向代理和正向代理

​		反向代理： 是指代理服务器接受 internet 上的请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给 internet 上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器；

​		正向代理：比如：我是一个用户，我访问不了某网站，但是我能访问一个代理服务器，这个代理服务器，它能访问我那个不能访问的网站，于是我先连上代理服务器，告诉它我需要那个无法访问网站的内容，代理服务器去取回来，然后返回给我。

## 1.3 nginx的主要功能

* 反向代理
* 请求转发
* 动静分离
* 静态网站部署
* 虚拟主机

## 1.4 nginx的配置文件

位置: `/nginx/con/nginx.conf`

```tex
> #配置worker进程运行用户 nobody也是一个linux用户，一般用于启动程序，没有密码
> user  nobody;  
> #配置工作进程数目，根据硬件调整，通常等于CPU数量或者2倍于CPU数量
> worker_processes  1;  
>
> #配置全局错误日志及类型，[debug | info | notice | warn | error | crit]，默认是error
> error_log  logs/error.log;  
> #error_log  logs/error.log  notice;
> #error_log  logs/error.log  info;
>
> pid        logs/nginx.pid;  #配置进程pid文件 
>
>
> ###====================================================
>
> #配置工作模式和连接数
> events {
>  worker_connections  1024;  #配置每个worker进程连接数上限，nginx支持的总连接数就等于worker_processes * worker_connections
> }
>
> ###===================================================
>
> #配置http服务器,利用它的反向代理功能提供负载均衡支持
> http {
>  #配置nginx支持哪些多媒体类型，可以在conf/mime.types查看支持哪些多媒体类型
>  include       mime.types;  
>  #默认文件类型 流类型，可以理解为支持任意类型
>  default_type  application/octet-stream;  
>  #配置日志格式 
>  #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
>
> #log_format  main '$status $body_bytes_sent "$http_referer" '
>
> #log_format  main  ''"$http_user_agent" "$http_x_forwarded_for"';
>
>  #配置access.log日志及存放路径，并使用上面定义的main日志格式
>  #access_log  logs/access.log  main;
>
>  sendfile        on;  #开启高效文件传输模式
>  #tcp_nopush     on;  #防止网络阻塞
>
>  #keepalive_timeout  0;
>  keepalive_timeout  65;  #长连接超时时间，单位是秒
>
>  #gzip  on;  #开启gzip压缩输出
>
>  ###-----------------------------------------------
>
> 
>
>  #配置虚拟主机
>  server {
>      listen       80;  #配置监听端口
>      server_name  localhost;  #配置服务名
>
>      #charset koi8-r;  #配置字符集
>     
>      #access_log  logs/host.access.log  main;  #配置本虚拟主机的访问日志
>
>  #默认的匹配斜杠/的请求，当访问路径中有斜杠/，会被该location匹配到并进行处理
>      location / {
>      #root是配置服务器的默认网站根目录位置，默认为nginx安装主目录下的html目录
>          root   html;  
>      #配置首页文件的名称
>          index  index.html index.htm;  
>      }		
>
>      #error_page  404              /404.html;  #配置404页面
>      # redirect server error pages to the static page /50x.html
>      #error_page   500 502 503 504  /50x.html;  #配置50x错误页面
>
>  #精确匹配
>  location = /50x.html {
>          root   html;
>      }
>
>  	#PHP 脚本请求全部转发到Apache处理
>  	 # proxy the PHP scripts to Apache listening on 127.0.0.1:80
>  	 #
>  	 #location ~ \.php$ {
>  	 #    proxy_pass   http://127.0.0.1;
>  	 #}
>  	
>  	#PHP 脚本请求全部转发到FastCGI处理
>  	 # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
>  	 #
>  	 #location ~ \.php$ {
>  	 #    root           html;
>  	 #    fastcgi_pass   127.0.0.1:9000;
>  	 #    fastcgi_index  index.php;
>  	 #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
>  	 #    include        fastcgi_params;
>  	 #}
>  	
>  	#禁止访问 .htaccess 文件
>  	 # deny access to .htaccess files, if Apache's document root
>  	 # concurs with nginx's one
>  	 #
>  	 #location ~ /\.ht {
>  	 #    deny  all;
>  	 #}
>  	 }
>  	

#配置另一个虚拟主机
# another virtual host using mix of IP-, name-, and port-based configuration
#
#server {
#    listen       8000;
#    listen       somename:8080;
#    server_name  somename  alias  another.alias;

#    location / {
#        root   html;
#        index  index.html index.html
#    }




> ```conf
> #配置https服务，安全的网络传输协议，加密传输，端口443，运维来配置
> #
> # HTTPS server
> #
> #server {
> #    listen       443 ssl;
> #    server_name  localhost;
> 
> #    ssl_certificate      cert.pem;
> #    ssl_certificate_key  cert.key;
> 
> #    ssl_session_cache    shared:SSL:1m;
> #    ssl_session_timeout  5m;
> 
> #    ssl_ciphers  HIGH:!aNULL:!MD5;
> #    ssl_prefer_server_ciphers  on;
> 
> #    location / {
> #        root   html;
> #        index  index.html index.htm;
> #    }
> #}
```



## 1.5 负载均衡

### 1.5.1 实现负载均衡的方式

* 硬件负载均衡

  比如 F5、深信服、Array 等

  * 优点:  是有厂商专业的技术服务团队提供支持，性能稳定 
  * 缺点 :  是费用昂贵，对于规模较小的网络应用成本太高

  

* 软件负载均衡

  比如 Nginx、LVS、HAProxy 等

  * 优点是免费开源，成本低廉

### 1.5.2 开始配置

***Nginx通过在nginx.conf文件进行配置即可实现负载均衡***

原理图: 

​	![image-20220325172853620](C:\Users\孙肖飞\AppData\Roaming\Typora\typora-user-images\image-20220325172853620.png)





   

<u>配置步骤</u>:

  1. 在http 模块加上 upstream 配置

     ```tex
     upstream www.myweb.com { 
          	server  127.0.0.1:9100 weight=3; 
           	server  127.0.0.1:9200 weight=1;  
     } 
     ```

     > **其中weight=1表示权重，用于后端服务器性能不均的情况，访问比率约等于权重之比，权重越大访问机会越多**
     >
     > **upstream** 是配置nginx **与后端服务器负载均衡非常重要的一个模块**，并且它还能对后端的服务器的健康状态进行检查，若后端服务器中的一台发生故障，则前端的请求不会转发到该故障的机器

  2. 在server模块里添加location，并配置proxy_pass

     ```tex
     location /myweb {
     	proxy_pass http://www.myweb.com;
     }
     ```

     > **注意** : http后面的 www.myweb.com 要和 upstream 后面的一致

### 1.5.3 负载均衡策略

1. 轮询(默认)

   注意：这里的轮询并不是每个请求轮流分配到不同的后端服务器，与ip_hash 类似，但是按照访问url的hash结果来分配请求，使得每个url定向到同一个后端服务器，主要应用于后端服务器为缓存时的场景下**。**如果后端服务器down掉，将自动剔除

   ```tex
   upstream backserver { 
       	server 127.0.0.1:8080; 
       	server 127.0.0.1:9090; 
   } 
   ```

2. 权重 

   **每个请求按一定比例分发到不同的后端服务器，weight值越大访问的比例越大，用于后端服务器性能不均的情况**

   ```tex
   upstream backserver { 
       	server 192.168.0.14 weight=5; 
       	server 192.168.0.15 weight=2; 
   } 
   ```

3. ip_hash

   **ip_hash** 也叫IP绑定，每个请求按访问ip的hash值分配，这样每个访问客户端会固定访问一个后端服务器，可以解决会话Session丢失的问题

   算法: **算法：hash("124.207.55.82") % 2 = 0, 1**

   ```tex
   upstream backserver { 
       	ip_hash; 
       	server 127.0.0.1:8080; 
       	server 127.0.0.1:9090; 
   }
   ```

4. 最少连接

   **web** **请求会被转发到连接数最少的服务器上**

   ```tex
   upstream backserver { 
       	least_conn;
       	server 127.0.0.1:8080; 
       	server 127.0.0.1:9090; 
   }	
   
   ```

### 1.5.4 负载均衡的其他几个配置

1. 配置1

   ```tex
   upstream backserver { 
       	server 127.0.0.1:9100;
   		#其它所有的非backup机器down的时候，才请求backup机器
       	server 127.0.0.1:9200 backup; 
   } 
   
   ```

2. 配置2

   ```tex
   upstream backserver { 
   server 127.0.0.1:9100;
   #down表示当前的server是down状态，不参与负载均衡
       server 127.0.0.1:9200 down; 
   } 
   
   ```

> 一般在项目上线的时候，可以分配部署不同的服务器上，然后对Nginx重新reload。
>
> reload不会影响用户的访问，或者可以给一个提示页面,系统正在升级...



## 1.6 静态代理

把所有静态资源的访问改为访问nginx，而不是访问tomcat，这种方式叫静态代理。因为nginx更擅长于静态资源的处理，性能更好，效率更高。所以在实际应用中，我们将静态资源比如图片、css、html、js等交给nginx处理，而不是由tomcat处理。

### 1.6.1 实现方式1

 在nginx.conf的location中配置静态资源的后缀

**例如：当访问静态资源，则从linux** **服务器/opt/static** **目录下获取（举例**

```tex
location ~ .*\.(js|css|htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid
|doc|ppt|pdf|xls|mp3|wma)$ {
    	root /opt/static;
}

location后面的是匹配路径
整个配置表示以 .后面括号里面的这些后缀结尾的文件都由nginx处理
放置静态资源的目录，要注意一下目录权限问题，如果权限不足，给目录赋予权限； 
否则会出现403错误 chmod 755
```

### 1.6.2实现方式2

在nginx.conf的location中配置静态资源所在目录实现

**例如：当访问静态资源，则从linux** **服务器/opt/static** **目录下获取（举例**

```tex
location ~ .*/(css|js|img|images) {
    	root   /opt/static;
}
xxx/css
xxx/js
xxx/img
xxx/images
我们将静态资源放入 /opt/static 目录下，然后用户访问时由nginx返回这些静态资源

```



## 1.7 动静分离

Nginx的负载均衡和静态代理结合在一起，我们可以实现动静分离，这是实际应用中常见的一种场景。

动态资源，如jsp由tomcat或其他web服务器完成

静态资源，如图片、css、js等由nginx服务器完成

它们各司其职，专注于做自己擅长的事情

动静分离充分利用了它们各自的优势，从而达到更高效合理的架构

## 1.8 虚拟主机

主要有两种方式:

### 1.8.1 基于端口的虚拟主机

   基于端口的虚拟主机配置，使用端口来区分， 浏览器使用 同一个域名 + 端口 或 同一个 ip 地址 + 端口访问；

```tex
server{
	listen 8080;
	server_name www.myweb.com;
	localtion /myweb {
		proxy_pass http://www.myweb.com;
	}
}

server{
	listen 9090;
	server_name www.myweb.com;
	location /p2p {
		proxy_pass http://www.p2p.com;
	}
}
```



### 1.8.2 基于域名的虚拟主机（重点）

基于域名的虚拟主机是最觉的一种虚拟主机

```tex
server{
	listen 80;
	server_name www.myweb.com;
	locaton /myweb{
		proxy_pass http://www.myweb.com;
	}
}

server{
	listen 80;
	server_name www.myweb.com;
	location /myweb{
		proxy_pass http://www.p2p.com;
	}
}
```

> 需要修改一下本地的 hosts 文件 , 文件位置 : `C:\Windows\System32\drivers\etc\hosts` ，在 hosts文件配置：
>
> ​	ip    域名
>
> ​	ip    域名
>
> eg：
>
> ​	192.168.177.122 www.bmfdsj.com
>
> ​	192.168.177.122 www.fjdksfjf.com

