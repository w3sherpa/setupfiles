# Stand up sql server in ubuntu 22.04
1. Run docker command

[https://www.rabbitmq.com/install-debian.html follow the instruction carefully. Setting up repository based on linux distribution is the key.](https://www.cherryservers.com/blog/how-to-install-and-start-using-rabbitmq-on-ubuntu-22-04)


`sudo apt-get install curl gnupg apt-transport-https -y`  
`curl -1sLf "https://keys.openpgp.org/vks/v1/by-fingerprint/0A9AF2115F4687BD29803A206B73A36E6026DFCA" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/com.rabbitmq.team.gpg > /dev/null`  
`curl -1sLf "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0xf77f1eda57ebb1cc" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/net.launchpad.ppa.rabbitmq.erlang.gpg > /dev/null`  
`curl -1sLf "https://packagecloud.io/rabbitmq/rabbitmq-server/gpgkey" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/io.packagecloud.rabbitmq.gpg > /dev/null`  

Create a new file at /etc/apt/sources.list.d/rabbitmq.list and add the following repositories for ErLang and RabbitMQ respectively that are suited for Ubuntu 22.04 jammy release:
use command to create the file  
`sudo nano /etc/apt/sources.list.d/rabbitmq.list`
<br/><br/> 
Add following content to the file  
`deb [signed-by=/usr/share/keyrings/net.launchpad.ppa.rabbitmq.erlang.gpg] http://ppa.launchpad.net/rabbitmq/rabbitmq-erlang/ubuntu jammy main`  
`deb-src [signed-by=/usr/share/keyrings/net.launchpad.ppa.rabbitmq.erlang.gpg] http://ppa.launchpad.net/rabbitmq/rabbitmq-erlang/ubuntu jammy main`  
`deb [signed-by=/usr/share/keyrings/io.packagecloud.rabbitmq.gpg] https://packagecloud.io/rabbitmq/rabbitmq-server/ubuntu/ jammy main`  
`deb-src [signed-by=/usr/share/keyrings/io.packagecloud.rabbitmq.gpg] https://packagecloud.io/rabbitmq/rabbitmq-server/ubuntu/ jammy main`  

>>In order to set up an apt repository that provides the correct package, a few decisions have to be made. One is determining the distribution name. It often matches the Debian or Ubuntu release used:

jammy for Ubuntu 23.04
jammy for Ubuntu 22.04
focal for Ubuntu 20.04

`sudo apt-get update -y`  

>apt-get install -y erlang-base \  
    erlang-asn1 erlang-crypto erlang-eldap erlang-ftp erlang-inets \  
    erlang-mnesia erlang-os-mon erlang-parsetools erlang-public-key \  
    erlang-runtime-tools erlang-snmp erlang-ssl \  
    erlang-syntax-tools erlang-tftp erlang-tools erlang-xmerl

`sudo apt-get install rabbitmq-server -y --fix-missing` 

`sudo rabbitmq-plugins enable rabbitmq_management`

`rabbitmqctl add_user <usernamesudo> <password>`
`rabbitmqctl set_user_tags <username> administrator`
`rabbitmqctl set_permissions -p "/" "supersherpa" ".*" ".*" ".*"`  

1. Make sure following service and ports are open
    
    >`sudo ufw status`  
    `sudo ufw allow OpenSSH`
    `sudo ufw allow "Nginx HTTP"`  
    `sudo ufw allow "Nginx HTTPS"` 
    `sudo ufw allow 15672`
    `sudo ufw allow 5672`
    `sudo ufw allow 5671`      
    `sudo ufw status`  
    `sudo ufw enable`  

2. Useful cmds
   >sudo systemctl enable rabbitmq   
sudo systemctl start rabbitmq  
sudo systemctl status rabbtimq  
rabbitmq-plugins enable rabbitmq_management


   
