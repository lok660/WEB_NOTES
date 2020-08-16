# nginx

## 常用基本命令

```nginx
#重启nginx
service nginx restart
#停止
nginx -s stop
#优雅关闭
nginx -s quit
#nginx.conf配置文件修改后重制
nginx -s 0
#启动
nginx
#查看nginx状态
ps -A | grep nginx
```

## 基本配置

```nginx
server {
  listen 80;
  listen [::]:80;
  server_name _;
  root /var/www/html/base;
  index index.html;
  location / {
    try_files $uri $uri/ =404;
  }
}
server {
  listen 80; 监听端口号
  listen [::]:80;
  server_name _; 域名
  access_log  /var/www/html/base/host.access.log; 访问日志记录:用户ip 设配 访问时间
  root /var/www/html/base; 指定目录
  index index.html; 指定启动文件
  //错误配置
  error_page 404 /404.html;
  location = /404.html{
  deny 223.73.XXX.XXX; 禁止指定ip访问
  allow 223.73.XXX.XXX; 指定可访问 deny设置all 注意会从上往下执行 deny all 在前会导致allow匹配不成功
    root  404.html;
  }
  location / {
    proxy_pass ip; 反向代理
  }

}
```

