## Django 资源


### 插件

- [waitress] https://docs.pylonsproject.org/projects/waitress/en/stable/index.html
- [djangorestframework] https://www.django-rest-framework.org/
- [django-extra-settings] https://github.com/fabiocaccamo/django-extra-settings
- [whitenoise] http://whitenoise.evans.io/en/latest/
- [django-treebeard] https://django-treebeard.readthedocs.io/en/latest/

### CMS
- [wagtail] https://wagtail.org/
  
### 部署

- whitenoise serve 静态文件
- 使用 waitress 替代 gunicorn serve django， waitress 支持 linux，mac 和 windows
- 使用 node PM2 管理进程，开启多个进程，并在 nginx 配置 负载均衡，合理利用多核 CPU 资源
- 生产环境需要限流，优先使用 nginx 在网关入口处限流
- 开启头部安全性设置


#### Waitress serve django

waitress 既支持命令行，也支持 api 编程方式集成 django，在 django 项目目录下新建 `server.py` 文件，输入对应代码：

```python
from waitress import serve
from appadmin.wsgi import application
import os

port = os.environ.get('PORT', 8060)
serve(application, listen=f'*:{port}')
```

执行 `python server.py` 即可运行 django。

#### PM2 部署

采用 nodejs 生态的 `pm2` 进程管理器部署，通常生产环境采用独立的 python 虚拟环境运行，首先
切换到对应的虚拟环境，获取 python 解释器的路径：`which python`，PM2 配置 `ecosystem.config.js`:

```js
module.exports = {
    apps : [
        {
            name: "appadmin",
            cmd: "server.py",
            interpreter: "/root/miniconda3/envs/vangogh/bin/python",
            max_memory_restart: '256M',
            watch: false,
            env: {
                APP_ENV: "production",
                PORT: 8060,
            }
        },
        {
            name: "appadmin01",
            cmd: "server.py",
            interpreter: "/root/miniconda3/envs/vangogh/bin/python",
            max_memory_restart: '256M',
            watch: false,
            env: {
                APP_ENV: "production",
                PORT: 8061,
            }
        },
        {
            name: "appadmin02",
            cmd: "server.py",
            interpreter: "/root/miniconda3/envs/vangogh/bin/python",
            max_memory_restart: '256M',
            watch: false,
            env: {
                APP_ENV: "production",
                PORT: 8062,
            }
        }
    ],
};
```

使用 pm2 启动：`pm2 start ecosystem.config.js`，在 nginx 中配置负载均衡:

```nginx configuration
upstream appadmin {
    server 127.0.0.1:8060;
    server 127.0.0.1:8061;
    server 127.0.0.1:8062;
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

> **注：** 如果 django 使用了 session，cache 并且存储在 redis 或者 memcache 等应用缓存中，可以使用轮询方式，如果使用的是本地
文件存储，则需要改成 ip hash 的负载均衡方式，避免出现 session 错乱和缓存不命中等问题。
