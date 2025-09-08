# docker 开发者环境

## 统一网络

- 所有服务都在同一个网络中，方便服务之间的通信。

- docker-compose 参考配置：

```yaml
networks:
  app-network:
    driver: bridge
```

## nginx

- nginx 服务用于反向代理，将请求转发到不同的服务。
- 在挂载卷中配置`/Users/zouyl/env/volumes/nginx/nginx.conf`

```bash

user nginx;
worker_processes auto;

error_log /var/log/nginx/error.log notice;
pid /run/nginx.pid;


events {
    worker_connections 1024;
}


http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
    '$status $body_bytes_sent "$http_referer" '
    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main;

    sendfile on;
    #tcp_nopush     on;

    keepalive_timeout 65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

```

- docker-compose 参考配置

```yaml
nginx:
  image: nginx:latest
  # 开机启动
  restart: always
  ports:
    - 80:80
    - 443:443
  volumes:
    - /Users/zouyl/env/volumes/nginx/nginx.conf:/etc/nginx/nginx.conf
    - /Users/zouyl/env/volumes/nginx/conf.d:/etc/nginx/conf.d
    - /Users/zouyl/env/volumes/nginx/log:/var/log/nginx
    - /Users/zouyl/env/volumes/nginx/html:/usr/share/nginx/html
  networks:
    - app-network
```

## mysql

- mysql 服务用于数据库存储。
- 在挂载卷中配置`/Users/zouyl/env/volumes/mysql/my.cnf`

```bash
[mysqld]
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
pid-file=/var/run/mysqld/mysqld.pid
```

- docker-compose 参考配置

```yaml
mysql:
  image: mysql:latest
  # 开机启动
  restart: always
  environment:
    MYSQL_ROOT_PASSWORD: 123456
  ports:
    - 3306:3306
  volumes:
    - /Users/zouyl/env/volumes/mysql/data:/var/lib/mysql
    - /Users/zouyl/env/volumes/mysql/my.cnf:/etc/mysql/conf.d/my.cnf
    - /Users/zouyl/env/volumes/mysql/log:/var/log/mysql
  networks:
    - app-network
```
