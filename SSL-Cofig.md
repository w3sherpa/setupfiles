## Renew Expired namecheap cert ( only if its expired )
1. Install Open SSL if not installed.
2. Go to C:\Program Files\OpenSSL-Win64\bin>
3. openssl req -new -newkey rsa:2048 -nodes -keyout sherpaticket.key -out sherpaticket_csr.txt
4. Cert and Key will be created the in same bin folder.
5. Ressiue the cert in namecheap ui by uploading new CSR
6. Download the .crt and .ca-bundle and combine them in notepad (.crt content first and then .ca-bundle) and save as <service-name>.chained.crt
7. Copy this to the web server.
8. To empyt current cert content in web server user `echo "" <cert-file-name>.crt/key` and past the new value by using nano editor

## Copy files command from local to remote ubuntu
    `scp -rp C:/SSLCerts/sherpaticket.com/MoveToServer/* websherpa@192.168.1.19:/etc/nginx/ssl`
    `make sure to change websherpa to the name of the root or admin user`


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
