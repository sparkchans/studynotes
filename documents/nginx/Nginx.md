## Nginx 使用

### Nginx 使用场景

- 静态资源服务

  通过文件系统提供服务

- 反向代理服务

  缓存，负载均衡

- API 服务

  Web 防火墙

### 组成部分

- Nginx 可执行文件

- Nginx 配置文件

  该文件位于 /etc/nginx/nginx.conf 

- Nginx 访问日志

  该文件位于 /var/log/access.log

- Nginx 错误日志

  该文件位于 /var/log/error.log

### Nginx 常用命令

```shell
nginx -h

nginx -v

nginx -V

nginx -t

nginx -T

nginx -s signal
```

