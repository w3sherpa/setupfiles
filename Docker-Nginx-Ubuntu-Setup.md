1. sudo apt-get update
2. sudo apt-get install nginx
3. Install Docker
   1. https://docs.docker.com/engine/install/ubuntu/
4. Run  
   `sudo docker run --name app1 -p 8081:80 -d nginx`  
   will create app1 running on 8081  
   `sudo docker run --name app2 -p 8082:80 -d nginx`  
   will create app1 running on 8082
5. Runs `docker ps` to get list of new containers.
6. Update index file indie the contailer
    ><br>1. Run `docker exec -it <app-one-container-id> bash` to get inside of the container  
    >2. Run `sed -i 's/nginx/App-One/g' /usr/share/nginx/html/index.html` to update index.html. This command will replace all nginx word with App-One  
    >3. Run `exit` command to get out of the container
    >4. Repeat above step to update app2 index.html container with App-Two word.<br><br>
7. Add these site to Sites enabled:  
   > <br>1. Run `cd /etc/nginx/sites-enabled`  
   > 2. Run `sudo nano /etc/nginx/sites-enabled/apps.conf` to create a new conf fille  
   >3. Add following lines
   ><br>4. Run `systemctl restart nginx` to restart, `systemctl status nginx` to make sure nginx running
   >server {  
    listen        80;  
    server_name   chamalpitho.com *.chamalpitho.com;  
    location / {  
        proxy_pass         http://localhost:8081;  
        proxy_http_version 1.1;  
        proxy_set_header   Upgrade $http_upgrade;  
        proxy_set_header   Connection keep-alive;  
        proxy_set_header   Host $host;  
        proxy_cache_bypass $http_upgrade;  
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;  
        proxy_set_header   X-Forwarded-Proto $scheme;  
    }  
}  
server {  
    listen        80;  
    server_name   junaballa.com *.junaballa.com;  
    location / {  
        proxy_pass         http://localhost:8082;  
        proxy_http_version 1.1;  
        proxy_set_header   Upgrade $http_upgrade;  
        proxy_set_header   Connection keep-alive;  
        proxy_set_header   Host $host;  
        proxy_cache_bypass $http_upgrade;  
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;  
        proxy_set_header   X-Forwarded-Proto $scheme;  
    }  
}  

server {
    listen        80;
    server_name   dotnetsherpa.com *.dotnetsherpa.com;
    location / {
        proxy_pass         http://localhost:5601;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}

redirec traffic to https:
server {
    listen 80;
    server_name   sherpaticket.com *.sherpaticket.com;  
    return 301 https://www.sherpaticket.com$request_uri;
}

# Make sure to restart nxing
`sudo systemctl restart nginx`

8. Update dns ( namecheap ) to point the server_names in above setting to the IP address of the server.

# Stand up sql server in ubuntu 20.04
1. Follow the instruction set  
   https://learn.microsoft.com/en-us/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver16

   Make sure to save the username and password.  
   Default user is sa
   

2. Make sure to enable ssh, and port 1433 in the ubuntu server
    
    >
    `sudo ufw status`  
    `sudo ufw allow "Nginx Full"`    
    `sudo ufw allow ssh`  
    `sudo ufw allow 1433`  
    `sudo ufw status`  
    `sudo ufw enable`  
    

3. Get public ip of the ubuntu server and connect from ssms
   
