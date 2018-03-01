# Nginx

### Install
```bash
sudo apt-get install nginx -y
```

### Nginx config
```
#Edit  /etc/nginx/sites-enabled/default
server {
    listen 80 default_server;
    listen [::]:80 default_server;
    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    server_name localhost;
    proxy_send_timeout 300s;
    proxy_read_timeout 600s;

    location / {
        proxy_pass http://localhost:9080;
    }
}
```
```
server {
    listen       8080;
    server_name  localhost;

    gzip on;
    gzip_static on;
    gunzip on;

    proxy_send_timeout 300s;
    proxy_read_timeout 600s;

    location / {
            proxy_pass http://localhost:4000;
    }

    location /api {

        if ($request_method = 'OPTIONS') {

                add_header 'Access-Control-Allow-Origin' '*';
                add_header 'Access-Control-Allow-Credentials' 'true';
                add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';

                #
                # Custom headers and headers various browsers *should* be OK with but aren't
                #

                add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';

                #
                # Tell client that this pre-flight info is valid for 20 days
                #

                add_header 'Access-Control-Max-Age' 1728000;
                add_header 'Content-Type' 'text/plain charset=UTF-8';
                add_header 'Content-Length' 0;

                return 204;
        }
        if ($request_method = 'POST') {

                add_header 'Access-Control-Allow-Origin' '*';
                add_header 'Access-Control-Allow-Credentials' 'true';
                add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
                add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';

        }

        if ($request_method = 'GET') {

                add_header 'Access-Control-Allow-Origin' '*';
                add_header 'Access-Control-Allow-Credentials' 'true';
                add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
                add_header 'Access-Control-Allow-Headers' 'NT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                add_header 'Access-Control-Expose-Headers' 'Link, X-dashboard-Total-Count';

        }
        
        gunzip on;
        proxy_pass http://localhost:8080/api;

        client_max_body_size 50m;
        client_body_buffer_size 10m;

        set $max_chunk_size  10240;
        set $max_body_size  52428800;

        rewrite_by_lua_file /etc/nginx/decompress_body.lua;
    }
}
```

/etc/nginx/decompress_body.lua

see [Nginx decompress gzipped request](https://gist.github.com/iammehrabalam/19452f57c611895e5e5b175cffe3dee0)


### Modify the gzip config

Edit  /etc/nginx/nginx.conf.<br>
Uncomment all the  "Gzip Settings".

```
##
# Gzip Settings
##
gzip on;

gzip_disable "msie6";
gzip_vary on;
gzip_proxied any;
gzip_comp_level 6;
gzip_buffers 16 8k;
gzip_http_version 1.1;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```

```
# Start the nginx
sudo service nginx start
sudo service nginx restart
sudo service nginx reload     #if the nginx is already start, please reload
```