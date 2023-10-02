1. sudo mkdir /etc/nginx/ssl
2. copy ssl files
3. add following block to nginx

server {
    listen 443 ssl;
    server_name www.sherpaticket.com;
    ssl_certificate    	/etc/nginx/ssl/sherpaticket/sherpaticket.chained.crt;
    ssl_certificate_key	/etc/nginx/ssl/sherpaticket/sherpaticket.key;
    ssl_session_cache    shared:SSL:1m;
    ssl_session_timeout    5m;
    ssl_protocols    	TLSV1.1 TLSV1.2 TLSV1.3;
    ssl_ciphers    	HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers	on;
    access_log	/var/log/nginx/nginx-sherpaticket.log;
    location / {
   	 proxy_set_header	Host $host;
   	 proxy_set_header	X-Real-IP @remote_addr;
   	 proxy_set_header	X-Forwarded-For $proxy_add_X_forwarded_for;
   	 proxy_set_header	X-Forwarded-Proto $scheme;

   	 proxy_pass	http://192.168.1.73:80;
   	 proxy_ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    }
     	if ($request_method !~ ^(GET|HEAD|POST)$)
     	{
        	return 405;
    }
}
