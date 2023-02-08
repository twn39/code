## Openresty 

### 基本配置

```nginx configuration
user  nginx;
worker_processes  auto;

error_log  logs/error.log;

pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    charset        utf-8;
    sendfile       on;
    tcp_nopush     on;
    tcp_nodelay    on;
    client_max_body_size 5m;
    more_set_headers 'Server: Microsoft-IIS';
    more_clear_headers 'X-Powered-By';
    types_hash_max_size    2048;
    types_hash_bucket_size 64;

    keepalive_timeout  65;

    gzip  on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;

    include /usr/local/openresty/nginx/conf/conf.d/*.conf;
}
```

### https 配置

```nginx configuration
upstream lives {
    server 127.0.0.1:8020;
}

server {
    listen 80;

    server_name lives.com;

    location / {
        return 301 https://lives.com$request_uri; # http 重定向到 https
    }
}

server {
    listen 443 ssl http2;
    ssl_certificate /etc/letsencrypt/live/lives.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/lives.com/privkey.pem;
    ssl_session_timeout 1d;
    ssl_session_cache  builtin:1000  shared:SSL:10m;
    ssl_protocols  TLSv1.2 TLSv1.3;
    ssl_ciphers "ECDHE-ECDSA-CHACHA20-POLY1305 ECDHE-RSA-CHACHA20-POLY1305 DHE-RSA-CHACHA20-POLY1305";
    ssl_ocsp off;
    ssl_prefer_server_ciphers on;

    server_name lives.com;
    proxy_set_header Host $host;

    location / {
        proxy_pass http://lives;
        proxy_http_version                 1.1;
        proxy_connect_timeout              60s;
        proxy_send_timeout                 60s;
        proxy_read_timeout                 60s;
        proxy_set_header Host              $host;
        proxy_set_header X-Real-IP         $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Host  $host:$server_port;
        proxy_set_header X-Forwarded-Port  $server_port;
    }
}
```

### 限流

```nginx configuration
limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;
 
server {
    location /login/ {
        limit_req zone=mylimit;
        
        proxy_pass http://my_upstream;
    }
}
```

在上面定义了 rate 为：`10r/s`，burst 相当于一个 queue，当请求超过 `10r/s` 的时候，前 10 个请求会正常返回，后面的 20 个请求会进入 queue ，并且会以 `10r/s` 的出队列，再之后的所有请求都会被拒绝。

### 负载均衡

```nginx configuration
upstream appadmin {
    ip_hash; # 负载均衡策略, least_conn;
    server 127.0.0.1:8060 weight=5 max_fails=3 fail_timeout=5; # 权重，健康检查
    server 127.0.0.1:8061;
    server 127.0.0.1:8062 backup;
}

server {
    listen       80;
    server_name  localhost;

    location / {
        proxy_pass http://appadmin;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Host $host:$server_port;
        proxy_set_header X-Forwarded-Port $server_port;
    }
}
```

负载均衡策略：Round Robin(轮询，默认选项)，Least Connections(最少链接)，IP Hash(IP 地址)

### CORS 头部设置

``` 
Access-Control-Allow-Origin: https://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Allow-Credentials: true
Access-Control-Max-Age: 86400
Vary: Accept-Encoding, Origin
```

* `Access-Control-Allow-Origin`: 只能设置 `*` 和具体地址，无法使用模式匹配
* `Access-Control-Allow-Headers`: 需要将自定义添加的头部都写上，通常需添加 ajax 的头：`X-REQUEST-WITH`
* `Access-Control-Allow-Credentials`: `XMLHttpRequest` 和 `fetch` 在跨域访问的时候默认不会带上 cookie
* `Access-Control-Max-Age`: 设置预检缓存，减少预检请求

文档参考：https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS

由于 `Access-Control-Allow-Origin` 的限制，在 openresty 中配置 cors 功能有限，无法实现白名单域名列表，需要借助 lua
根据 host 动态匹配，然而使用 lua 灵活性不高，因此建议在应用程序端，根据白名单列表匹配 host，动态生成：`Access-Control-Allow-Origin`。

还有一种方法是前端增加 BFF，通过 nodejs 反向代理接口，绕过跨域问题，这对于使用 k8s 内部部署了很多的微服务，
前端需要整合很多微服务才能实现对应功能的情况下非常方便。


