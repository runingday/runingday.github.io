# 参考文档
https://mozilla.github.io/server-side-tls/ssl-config-generator/

<pre>
upstream sensors_data_web {
        server 192.168.1.181:8107;
        server 192.168.1.182:8107;                                                                                                   server 192.168.1.183:8107;                                                                                                   ip_hash;
}
server {
        listen 80;
        server_name shence.ngmm365.com;
        return 301 https://$host:4107$request_uri;
}
server {
# 端口号，可自定义。集群版建议 8106，单机版建议 8006
        listen 4106;
# 域名，根据实际情况填写
        server_name shence.ngmm365.com;

# 以下配置如果开启 ssl 则需要
        ssl on;

        ssl_certificate /home/sa_cluster/sa/nginx/conf.d/2018/server.cer;
        ssl_certificate_key /home/sa_cluster/sa/nginx/conf.d/2018/server.key;
        ssl_session_timeout 1d;
        ssl_session_cache shared:SSL:50m;
        ssl_session_tickets off;

# Diffie-Hellman parameter for DHE ciphersuites, recommended 2048 bits
        #ssl_dhparam /path/to/dhparam.pem;

# intermediate configuration. tweak to your needs.
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS';
        ssl_prefer_server_ciphers on;
# ssl_certificate /etc/ssl/fullchain.pem;     # 配置 ssl 证书信任链
# ssl_certificate_key /etc/ssl/privkey.pem;   # 配置 ssl 私钥

</pre>
