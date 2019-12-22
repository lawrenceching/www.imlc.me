# Nginx Permission denied while connecting to upstream 解决办法

今天配置新服务器的时候，发现 Nginx 无法正常代理请求。请求反向代理的地址时页面显示 "504 Bad Gateway"。

查看 `/var/log/nginx/error.log`, 发现如下错误
```
2019/12/18 08:11:35 [crit] 10796#0: *10 connect() to 127.0.0.1:9200 failed (13: Permission denied) while connecting to 
upstream, client: 10.0.0.217, server: , request: "GET / HTTP/1.1", upstream: "http://127.0.0.1:9200/", host: "10.0.0.100:
8080"
```

再查看 `/var/log/audit/audit.log`

```
cat /var/log/audit/audit.log | grep nginx | grep denied
```

发现一系列如下日志

```
t=system_u:system_r:httpd_t:s0 tcontext=system_u:object_r:wap_wsp_port_t:s0 tclass=tcp_socket permissive=0
type=AVC msg=audit(1576674696.863:116775): avc:  denied  { name_connect } for  pid=10795 comm="nginx" dest=9200 scontext=system_u:system_r:httpd_t:s0 tcontext=system_u:object_r:wap_wsp_port_t:s0 tclass=tcp_socket permissive=0
type=AVC msg=audit(1576674697.016:116776): avc:  denied  { name_connect } for  pid=10795 comm="nginx" dest=9200 scontext=system_u:system_r:httpd_t:s0 tcontext=system_u:object_r:wap_wsp_port_t:s0 tclass=tcp_socket permissive=0
```

意识到问题处在了 SELinux 上。通过如下命令，你可以
```
setsebool -P httpd_can_network_connect 1
```

Nginx 在 SELinux 系统中打上了"httpd_t"标签，并不能反向代理到同一主机上的上游服务。具体情况可以参考 Nginx 官方写的博客:  
https://www.nginx.com/blog/using-nginx-plus-with-selinux/

## Reference

https://stackoverflow.com/questions/23948527/13-permission-denied-while-connecting-to-upstreamnginx  
https://www.redhat.com/en/topics/linux/what-is-selinux  
https://www.nginx.com/blog/using-nginx-plus-with-selinux/  
