#
event epoll

反向代理时最大连接数 = worker_connections（上限为nofile）* worker_processes /2
反向代理时占用两个连接

### response server字段隐藏nginx版本号
```
server_tokens off;
```
### ssl安全优化
https 安全检测网站：`myssl.com`

添加nginx安全参数：
```
    	#限制一些不安全的ssl协议，以下是启用的安全协议
        ssl_protocols TLSv1.1 TLSv1.2;
        #定义加密套件，排除了安全性不佳、性能低下的加密算法。加密套件的顺序是非常重要的，因为其决定了优先选择哪个算法。
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4:!DH:!DHE;
```
之前openssl版本为1.0.1e ，存在重大安全漏洞，已升级到1.0.2k版本。

```
[root@intel-web01 conf.d]# openssl version -a
OpenSSL 1.0.2k-fips  26 Jan 2017
```
升级方法：

因nginx为yum安装，将nginx卸载后重新安装即可，nginx1.12.2版本已重新更新了openssl依赖版本，会自动把openssl也更新了。
```
cp -rp /etc/nginx /etc/nginx_bak
yum remove --nodeps nginx
yum install nginx
rm -rf /etc/nginx && mv /etc/nginx_bak /etc/nginx
systemctl restart nginx
```






### remote_addr更改为用户真实IP

```
        set_real_ip_from  0.0.0.0/0;
        real_ip_header    X-Forwarded-For;
        real_ip_recursive on;

```
### 缓存控制

对html文件禁止缓存，其余文件允许缓存

示例：
```
    location / {
        alias /data/xxx/sites/xxx/;
        index index.html;
        if ($request_filename ~ .*\.(htm|html)$)
        {
            add_header Cache-Control no-store;
        }
    }
```
### .htaccess, .htpasswd
```
        location ~ /\.ht* {
                deny  all;
				access_log off;
				log_not_found off;
        } 
```

```
    location = / {
        default_type text/html;
        access_log off;
        return 200 'It works';
    }
```

### php fastcgi

```
    location ~* ^/ma-api/(.*\.php) {
        set $real_script_name $1;
        fastcgi_pass   unix:/dev/shm/php-fpm.sock;
        fastcgi_index   index.php;

        include fastcgi_params;
        #set $real_script_name $fastcgi_script_name;
        if ($fastcgi_script_name ~ "^/ma-api/(.+\.php)(/.+)$") {
            set $real_script_name $1;
            set $path_info $2;
        }   
        fastcgi_param SCRIPT_FILENAME /data/a2/sites/ma-api/public/$real_script_name;
        fastcgi_param SCRIPT_NAME $real_script_name;
        fastcgi_param PATH_INFO $path_info;
    }

```
### proxy_pass
```
upstream td-campaign {
    server         172.17.5.16:28004 max_fails=10 fail_timeout=20s weight=100;
    server         172.17.5.16:28005 max_fails=10 fail_timeout=20s weight=100;
    keepalive      50;
}

...
    proxy_redirect          off;
    proxy_set_header   Host             $host;
    proxy_set_header   X-Real-IP        $remote_addr;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
    proxy_set_header   X-Request-Id     $request_id;
    proxy_cache_use_stale   error timeout invalid_header updating http_500 http_502 http_503 http_504;

    proxy_connect_timeout   300s;
    proxy_send_timeout      300s;
    proxy_read_timeout      300s;

    client_max_body_size     200m;
    client_body_buffer_size 1024k;
    proxy_buffer_size 32k;
    proxy_buffers 64 32k;

    proxy_http_version      1.1;
    proxy_set_header        Connection "";
    
    
    location ~* ^/insight/.+ {
        proxy_pass              http://td-insight;
        proxy_http_version      1.1;
        }
```

### default

```
        error_page   404 = /404.html;
        error_page   500 502 503 504  = /50x.html;
        
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
        
        location = /404.html {
            root   /usr/share/nginx/html;
        }

        location = /error {
            root   /usr/share/nginx/html;
        }
        
        location ~* \.(txt|doc|MD|md)$ { 
			root /usr/share/nginx/html; 
			deny all; 
		}
        
        location = /robots.txt {
            root /usr/share/nginx/html;
            allow all;
			log_not_found off;
			access_log off;
        }
        
        location = /favicon.ico {
			log_not_found off;
			access_log off;
		}
    
    
```
