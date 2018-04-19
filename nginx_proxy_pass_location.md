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



