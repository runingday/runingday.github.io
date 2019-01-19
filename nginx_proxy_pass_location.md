# Nginx配置proxy_pass转发的/路径问题

<pre>
在nginx中配置proxy_pass时，如果是按照^~匹配路径时,要注意proxy_pass后的url最后的/,当加上了/，相当于是绝对根路径，则nginx不会把location中匹配的路径部分代理走;如果没有/，则会把匹配的路径部分也给代理走。
location ^~ /static_js/ 
{ 
proxy_cache js_cache; 
proxy_set_header Host js.test.com; 
proxy_pass http://js.test.com/; 
}
如上面的配置，如果请求的url是http://servername/static_js/test.html
会被代理成http://js.test.com/test.html
而如果这么配置
location ^~ /static_js/ 
{ 
proxy_cache js_cache; 
proxy_set_header Host js.test.com; 
proxy_pass http://js.test.com; 
}
则会被代理到http://js.test.com/static_js/test.htm
当然，我们可以用如下的rewrite来实现/的功能
location ^~ /static_js/ 
{ 
proxy_cache js_cache; 
proxy_set_header Host js.test.com; 
rewrite /static_js/(.+)$ /$1 break; 
proxy_pass http://js.test.com; 
} 
</pre>

<pre>
用户做转发配置想要实现
1） http://xxx.xxx.xxx/test/senn/sa  --> http://xxx.xxx.xxx:1880/sa
2） http://xxx.xxx.xxx/test/senn/debug  --> http://xxx.xxx.xxx:1880/debug
3) http://xxx.xxx.xxx/test/senn/t/a  --> http://xxx.xxx.xxx:1880/t/a
4) http://xxx.xxx.xxx/test/senn/t/A  --> http://xxx.xxx.xxx:1880/t/A
location /test/senn/ {
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_pass http://xxx.xxx.com:1880/;
      proxy_ignore_client_abort on;
      proxy_set_header Host xxx.xxx.com;
      proxy_http_version 1.1;
      proxy_set_header Connection "";
  }

</pre>
# Nginx reverse proxy trailing slash
I am having a node app which is running on port 3000. I am using nginx to reverse proxy. Now the application only works if there is a slash in the end.

So this works: https://example.com/main/site/react/

This does not: https://example.com/main/site/react

Here is the configuration which I am using:
<pre>
location /main/site/react {
    error_log /var/log/nginx/app.error.log;
    rewrite ^/main/site/react/(?:|[\w\/]+)(\/static.*)$ $1 break;
    rewrite ^/main/site/react/?(.*)$ /$1 break;
    proxy_pass http://127.0.0.1:3000/;
}
</pre>
After checking other related questions, adding following rewrite rule(after error_log) worked for me:

<pre> rewrite ^(.*[^/])$ $1/ permanent; </pre>
Above rule rendered my node application, but it failed with my static resources. I have some static resources rewrites which are not working.

So this URL is not working: https://example.com/main/site/react/static/js/bundle.js. It always renders index HTML page.

<pre>
location /main/site/react {
    rewrite ^/main/site/react$ https://$host/main/site/react/ permanent;
    rewrite ^/main/site/react/(?:|[\w\/]+)(\/static.*)$ $1 break;
    rewrite ^/main/site/react/?(.*)$ /$1 break;
    proxy_pass http://127.0.0.1:3000;
}
</pre>

# examples for how does nginx proxy_pass map the request

The proxy_pass directive sets the address of the proxied server and the URI to which location will be mapped.

Here are some examples to show how the request URI will be mapped.

The version of nginx:

nginx version: nginx/1.4.2
The server config:

#server config
server {
    listen        80;
    server_name   test.com;
}
2. location without regular expression
If the proxy_pass directive is specified without a URI,

location /app/ {
    proxy_pass      http://192.168.154.102;
}

test.com/app/xxxxx =>  http://192.168.154.102/xxxxx
If the proxy_pass directive is specified with a URI:

location /app/ {
    proxy_pass      http://192.168.154.102/maped_dir/;
}

test.com/app/xxxxx =>  http://192.168.154.102/maped_dir/xxxxx
Forward the requested Host header

By default, the Host header from the request is not forwarded, but is set based on the proxy_pass statement. To forward the requested Host header, it is necessary to use:

proxy_set_header Host $host;
3. location with regular expression
If the location is given by regular expression, can not be a URI part in proxy_pass directive, unless there are variables in the directive.

location  ~ ^/app/(.*)$ {
    # proxy_pass   http://127.0.0.1/some_dir;       # error
    proxy_pass   http://127.0.0.1/some_dir/$1;      # ok
}
Update from the comment by @Chris Young

variables in proxy_pass directive:

location ~ ^/app/(.*)$ {
    proxy_pass       http://192.169.154.102:$1;
}

test.com/app/8081 => http://192.168.154.102:8081
and:

location ~ ^/app/(.*)$ {
    proxy_pass       http://192.169.154.102:9999/some_dir/$1;
}

test.com/app/request/xxxxx => http://192.168.154.102:9999/some_dir/request/xxxxx
with a rewrite directive in the location:

If the rewrite rule is hit, the URI specified in the directive is ignored and the full changed request URI is passed to the server:

location  /app/ {
    rewrite    ^/app/hit/(.*)$ /hit_page.php?path=$1 break;
    proxy_pass   http://192.168.154.102:9999/some_dir/;
}
/app/hit/some/request/?name=xxxxx

=> http://192.168.154.102:9999/hit_page.php?path=some/request/&name=xxxxx

/app/not_hit/some/request/?name=xxxxx

=> http://192.168.154.102:9999/some_dir/not_hit/some/request/?name=xxxxx


