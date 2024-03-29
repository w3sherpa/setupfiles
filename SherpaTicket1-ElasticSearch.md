# Setup
### Get repository
#Install Java and Nginx
>sudo apt update
sudo apt install wget curl gnupg2 -y
sudo apt install openjdk-11-jdk -y
java -version
sudo apt install nginx -y


#Install and Configure Elasticsearch
>wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
sudo apt-get install apt-transport-https
echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
sudo apt update
sudo apt install elasticsearch -y
sudo nano /etc/elasticsearch/elasticsearch.yml


Add following lines  
Network Section
>`network.host: 0.0.0.0`  
`network.bind_host: 0.0.0.0`  
`network.publish_host: 0.0.0.0`  
`discovery.seed_hosts: ["0.0.0.0", "[::0]"]`

Security Configuration 
Following are disabled for faster commnunication and ease of configuration.
`xpack.security.enabled: false`
>xpack.security.http.ssl:
  enabled: false`


# Verify
`sudo systemctl start elasticsearch`
`sudo systemctl enable elasticsearch`
`sudo systemctl restart elasticsearch`

# UFW settings (make sure to enable other port like http,ssh,rabbit if they are in that server)

`sudo ufw allow OpenSSH`
`sudo ufw allow "Nginx HTTP"`  
`sudo ufw allow "Nginx HTTPS"` 
`sudo ufw allow 15672`
`sudo ufw allow 5672`
`sudo ufw allow 5671` 

### ELK
`sudo ufw allow 9200`
`sudo ufw allow 5601`  
`sudo ufw allow 9243` 

### Verify
`sudo ufw enable`
`sudo ufw status`

After installation make sure to get the password and the certificate finger print printed on the console (if it prints)

# Verify
`sudo systemctl start elasticsearch`
`sudo systemctl enable elasticsearch`
`sudo systemctl restart elasticsearch`

## Change elastic user password 

`cd /usr/share/elasticsearch/bin/`
`./elasticsearch-reset-password -u elastic`
The password will be printed in the console. Makesure to save it ( you will need it to log in to Kibana)
U:elastic:P:XStY3CNEOm9 [as of 12/09/2023]

## Get Enrollment Toke to use in kibana setting
1. `cd /usr/share/elasticsearch/bin/`
1. `sudo ./elasticsearch-create-enrollment-token --scope kibana`
Copy the token value and save in notepad
[eyJ2ZXIiOiI4LjExLjIiLCJhZHIiOlsiMTkyLjE2OC4xLjM4OjkyMDAiXSwiZmdyIjoiYmYwMDY5ODcxODg2NDY3OGM3YTNmNTg2ZmU2YzFhYmJhMzIyNTc2ODFiNGZjOTNmYTBkOTk1ZDNhYWM2ZDMyNSIsImtleSI6ImNIS2pUb3dCTXRCcEJ4ZjRsYklWOmFucTR4dFhyU2dtSGJiWm90U0V6dHcifQ==]

Go to 
`https://192.168.1.38:9200` should give you login window ( change ip) user username and pwd from Change elastic user password step above

## Install Kibana
For installing and configuring Kibana Dashboard, we don’t need to add any other repository because the packages are available through the already added ElasticSearch. Hence, just use the given command:
`sudo apt install kibana -y`
`sudo nano /etc/kibana/kibana.yml`
>Uncomment and modify the following lines from:
server.port: 5601
server.host: "localhost"
elasticsearch.hosts: ["http://localhost:9200"]
To:
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"]

# Verify
`sudo systemctl status kibana`
`sudo systemctl enable kibana`
`sudo systemctl restart kibana`
`sudo systemctl start kibana`

## Get enrollment code
`cd /usr/share/elasticsearch/bin/`
`./elasticsearch-create-enrollment-token --scope kibana`

## Get Verification code
`cd /usr/share/kibana/bin/`
` ./kibana-verification-code`


#Issues with SSL and configuring in Program.cs
1. Make sure to have fingerprint 
    Run
    `openssl x509 -fingerprint -sha256 -in /etc/elasticsearch/certs/http_ca.crt`
    Should return sha256 fingerprint and cert. Copy the Finger print value and put it in the setting 
>.WriteTo.Elasticsearch(
        new ElasticsearchSinkOptions(new Uri(context.Configuration["ElasticSearch:Uri"]))
        {
            //setting up warning level to elastic sink, file write and console can be set at different level
            TypeName = null,
            MinimumLogEventLevel= Serilog.Events.LogEventLevel.Warning,
            IndexFormat = $"{context.Configuration["ApplicationName"]}-logs-{DateTime.UtcNow:yyyy-MM}",
            AutoRegisterTemplate = true,
            AutoRegisterTemplateVersion = AutoRegisterTemplateVersion.ESv7,
            BatchAction = ElasticOpType.Create,
            EmitEventFailure = EmitEventFailureHandling.WriteToSelfLog |
                               EmitEventFailureHandling.WriteToFailureSink |
                               EmitEventFailureHandling.RaiseCallback,
            ModifyConnectionSettings = x => 
                x.BasicAuthentication(context.Configuration["ElasticSearch:User"], context.Configuration["ElasticSearch:Password"])
                .CertificateFingerprint("4E:45:89:B7:6D:A2:6B:74:E0:49:1C:98:5B:A5:E2:D2:8C:BA:BE:AE:D7:84:9E:2B:31:8F:9E:CD:64:AE:B0:27"),
            FailureSink = new RollingFileSink("./sherpaticket-serilog-failures.txt", new JsonFormatter(), null, 30)
        }); 
    