# Stand up sql server in ubuntu 22.04
1. Run docker command

https://www.rabbitmq.com/install-debian.html follow the instruction carefully. Setting up repository based on linux distribution is the key.



2. Make sure to enable ssh, and port 1433 in the ubuntu server
    
    >`sudo ufw status`  
    `sudo ufw allow OpenSSH`
    `sudo ufw allow "Nginx HTTP"`  
    `sudo ufw allow "Nginx HTTPS"` 
    `sudo ufw allow 15672`
    `sudo ufw allow 5672`
    `sudo ufw allow 5671`      
    ` sudo ufw status`  
    `sudo ufw enable`  

3. Get public ip of the ubuntu server and connect from ssms
   
