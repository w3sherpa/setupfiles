## Copy files command from local to remote ubuntu
    `scp -rp C:/SSLCerts/sherpaticket.com/MoveToServer/* ubuntu@192.168.1.12:/etc/nginx/ssl`


1. `sudo mkdir /etc/nginx/ssl`
    `sudo chmod -R 777 /etc/nginx/ssl`
2. Create key and crt file
    `sudo nano sherpaticket.key` and copy paste the key content
    `sudo nano www_sherpaticket_com_chain.crt` and copy past the crt content

3. `Make sure port forwarding on both 80 and 443 on router for home servers`

4. add following block to nginx



server {
    listen 443 ssl;
    server_name www.sherpaticket.com;
    ssl_certificate    	/etc/nginx/ssl/www_sherpaticket_com_chain.crt;
    ssl_certificate_key	/etc/nginx/ssl/sherpaticket.key;
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

   	 proxy_pass	http://localhost:8090;
   	 proxy_ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    }
     	if ($request_method !~ ^(GET|HEAD|POST)$)
     	{
        	return 405;
    }
}
