# Bash commands
```
aptitude install nginx-full
cd /etc/nginx/sites-available/
vi readmeproxy
cd ../sites-enabled/
ln -s /etc/nginx/sites-available/readmeproxy ./
nginx -t &&  /etc/init.d/nginx restart
```
# /etc/nginx/sites-available/readmeproxy
```
server {
        listen 80;

        server_name readmeproxy.com www.readmeproxy.com;

        location / {
                #DNS resolver for /etc/resolv.conf
                resolver 8.8.8.8;
                #CORS headers
                if ($request_method = 'OPTIONS') {
                        add_header 'Access-Control-Allow-Origin' '*';
                        add_header 'Access-Control-Allow-Credentials' 'true';
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
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
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                }
                #end of CORS headers

                #PROXY for HTTP proxyto header
                if ( $http_proxyto ~* ^(?:https?):\/\/(?:[^@:\/]*@)?([^:\/]+) ) {
                        set $proxyhost $1;
                }

                add_header 'X-proxyhost' $proxyhost;
                proxy_pass        $http_proxyto;
                proxy_http_version 1.1;
                proxy_redirect    off;
                proxy_read_timeout 120;
                proxy_set_header  Host                $proxyhost;
                proxy_set_header  X-Forwarded-For     $remote_addr;
        }

}
# HTTPS server

server {
        listen 443;
        server_name readmeproxy.com www.readmeproxy.com;
#       ssl on;
#       ssl_certificate cert.pem;
#       ssl_certificate_key cert.key;
#
#       ssl_session_timeout 5m;
#
#       ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
#       ssl_ciphers "HIGH:!aNULL:!MD5 or HIGH:!aNULL:!MD5:!3DES";
#       ssl_prefer_server_ciphers on;
#
        location / {
                try_files $uri $uri/ =404;
        }
}
```

# Verification
```
curl -v --header "proxyto: https://www.yahoo.com/" http://readmeproxy.com
curl -v  -X OPTIONS http://readmeproxy.com
```
