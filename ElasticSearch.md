# Setup
### Get repository
`curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg`  
`echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list`  
### Install
`sudo apt update`  
`sudo apt install elasticsearch`  

### Setting
`sudo nano /etc/elasticsearch/elasticsearch.yml`<br/>
Add following lines  
>`network.host: 0.0.0.0`  
`network.bind_host: 0.0.0.0`  
`network.publish_host: 0.0.0.0`  
`discovery.seed_hosts: ["0.0.0.0", "[::0]"]`

# Verify
`sudo systemctl start elasticsearch`
`sudo systemctl enable elasticsearch`
`sudo systemctl restart elasticsearch`

UFW settings (make sure to enable other port like http,ssh,rabbit if they are in that server)
`sudo ufw allow from 198.51.100.0 to any port 9200`
`sudo ufw enable`
`sudo ufw status`







