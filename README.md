# Bash commands to install nginx
```
aptitude install nginx-full
cd /etc/nginx/sites-available/
vi readmeproxy
cd ../sites-enabled/
ln -s /etc/nginx/sites-available/readmeproxy ./
nginx -t &&  /etc/init.d/nginx restart
```
# Bash commands to add SSL certs
```
mkdir -p /etc/nginx/ssl
cat readmeproxy_com.crt COMODORSADomainValidationSecureServerCA.crt COMODORSAAddTrustCA.crt AddTrustExternalCARoot.crt > cert_chain.crt
cp -v cert_chain.crt /etc/nginx/ssl/
cp -v server.key /etc/nginx/ssl/

```
# /etc/nginx/sites-available/readmeproxy
```
ssl_session_cache    shared:SSL:1m;
ssl_session_timeout  10m;

#debug logging
#log_format readmeproxy '$remote_addr - $remote_user [$time_local] '
#                       '"$request" $status $http_proxyto '
#                       '"$proxyhost" ';
#access_log  /var/log/nginx/readmeproxy.log readmeproxy;

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
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                        add_header 'Access-Control-Max-Age' 1728000;
                        add_header 'Content-Type' 'text/plain charset=UTF-8';
                        add_header 'Content-Length' 0;
                        return 204;
                }
                if ($request_method = 'POST') {
                        add_header 'Access-Control-Allow-Origin' '*';
                        add_header 'Access-Control-Allow-Credentials' 'true';
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                }
                if ($request_method = 'PUT') {
                        add_header 'Access-Control-Allow-Origin' '*';
                        add_header 'Access-Control-Allow-Credentials' 'true';
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                }
                if ($request_method = 'GET') {
                        add_header 'Access-Control-Allow-Origin' '*';
                        add_header 'Access-Control-Allow-Credentials' 'true';
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                }
                #end of CORS headers

                #PROXY for HTTP proxyto header
                if ( $http_proxyto ~* ^(?:https?):\/\/(?:[^@:\/]*@)?([^:\/]+) ) {
                        set $proxyhost $1;
                }

                #add_header 'X-proxyhost' $proxyhost;
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
        ssl on;
        ssl_certificate /etc/nginx/ssl/cert_chain.crt;
        ssl_certificate_key /etc/nginx/ssl/server.key;

        ssl_session_timeout 5m;

        ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
        ssl_ciphers "HIGH:!aNULL:!MD5 or HIGH:!aNULL:!MD5:!3DES";
        ssl_prefer_server_ciphers on;

        location / {
                #DNS resolver for /etc/resolv.conf
                resolver 8.8.8.8;
                #CORS headers
                if ($request_method = 'OPTIONS') {
                        add_header 'Access-Control-Allow-Origin' '*';
                        add_header 'Access-Control-Allow-Credentials' 'true';
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                        add_header 'Access-Control-Max-Age' 1728000;
                        add_header 'Content-Type' 'text/plain charset=UTF-8';
                        add_header 'Content-Length' 0;
                        return 204;
                }
                if ($request_method = 'POST') {
                        add_header 'Access-Control-Allow-Origin' '*';
                        add_header 'Access-Control-Allow-Credentials' 'true';
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                }
                if ($request_method = 'PUT') {
                        add_header 'Access-Control-Allow-Origin' '*';
                        add_header 'Access-Control-Allow-Credentials' 'true';
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                }
                if ($request_method = 'GET') {
                        add_header 'Access-Control-Allow-Origin' '*';
                        add_header 'Access-Control-Allow-Credentials' 'true';
                        add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, OPTIONS';
                        add_header 'Access-Control-Allow-Headers' 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type';
                }
                #end of CORS headers

                #PROXY for HTTP proxyto header
                if ( $http_proxyto ~* ^(?:https?):\/\/(?:[^@:\/]*@)?([^:\/]+) ) {
                        set $proxyhost $1;
                }

                #add_header 'X-proxyhost' $proxyhost;
                proxy_pass        $http_proxyto;
                proxy_http_version 1.1;
                proxy_redirect    off;
                proxy_read_timeout 120;
                proxy_set_header  Host                $proxyhost;
                proxy_set_header  X-Forwarded-For     $remote_addr;
        }
}

```

# Verification
```
curl -v --header "proxyto: https://www.yahoo.com/" http://readmeproxy.com
curl -v  -X OPTIONS http://readmeproxy.com
curl -v --header "proxyto: https://www.yahoo.com/" https://readmeproxy.com
curl -v  -X OPTIONS https://readmeproxy.com
```
