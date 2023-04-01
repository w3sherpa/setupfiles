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



# New SETUP
1. `sudo apt update && sudo apt upgrade`
2. `sudo apt install default-jdk` (this is for kibana)
3. `wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg`
4. `sudo apt-get install apt-transport-https`
5. `echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list`
6. `sudo apt update`
7. `sudo apt-get install elasticsearch`

After installation make sure to get the password and the certificate finger print printed on the console (if it prints)

# Verify
`sudo systemctl start elasticsearch`
`sudo systemctl enable elasticsearch`
`sudo systemctl restart elasticsearch`

## Change elastic user password
`cd /usr/share/elasticsearch/bin/`
`./elasticsearch-reset-password -u elastic`
The password will be printed in the console. Makesure to save it ( you will need it to log in to Kibana)
B0d8=itqK0YejqvSk4DL

Go to 
`https://104.237.140.169:9200` should give you login window ( change ip)

## Install Kibana
For installing and configuring Kibana Dashboard, we don’t need to add any other repository because the packages are available through the already added ElasticSearch. Hence, just use the given command:
`sudo apt install kibana`

# Verify
`sudo systemctl status kibana.service`
`sudo systemctl enable kibana.service`
`sudo systemctl restart kibana.service`
`sudo systemctl start kibana.service`

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
    