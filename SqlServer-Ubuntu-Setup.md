# Stand up sql server in ubuntu 20.04
1. Follow the instruction set  
   https://learn.microsoft.com/en-us/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver16

   Make sure to save the username and password.  
   Default user is sa
   

2. Make sure to enable ssh, and port 1433 in the ubuntu server
    
    >`sudo ufw status`  
    `sudo ufw allow ssh`  
    `sudo ufw allow 1433`  
    ` sudo ufw status`  
    `sudo ufw enable`  

3. Get public ip of the ubuntu server and connect from ssms
   
