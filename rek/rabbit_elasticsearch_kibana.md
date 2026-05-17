# SherpaTicket Elasticsearch + Kibana Docker Setup

This README explains how to run the Elasticsearch, Elasticsearch init, and Kibana containers on the existing Docker network:

```text
sherpaticket_net
```

The four containers are:

```text
rabbitmq_sherpaticket # for rabbitmq
sherpaticket_elasticsearch_init   # Optional Linux permission-fix container
sherpaticket_elasticsearch        # Elasticsearch
sherpaticket_kibana               # Kibana
```

---

## 1. Prerequisites

Make sure Docker and Docker Compose are installed.

Verify the existing Docker network exists:

```bash
docker network ls | grep sherpaticket_net
```

If it does not exist, create it:

```bash
docker network create sherpaticket_net
```

On Linux, Elasticsearch also needs this kernel setting:

```bash
sudo sysctl -w vm.max_map_count=262144
```

To make it permanent:

```bash
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

Check the current value:

```bash
sysctl vm.max_map_count
```

---

## 2. Elasticsearch `.env`

Create an `.env` file in the Elasticsearch Compose directory. Or user file transfer tool like filezilla or WinScp.

```env
ELASTIC_VERSION=9.4.0
ELASTIC_CONTAINER_NAME=sherpaticket_elasticsearch
ELASTIC_NODE_NAME=sherpaticket-es01
ELASTIC_CLUSTER_NAME=sherpaticket-cluster

ELASTIC_USERNAME=elastic
ELASTIC_PASSWORD=yourStrongElasticPasswordHere

ELASTIC_HTTP_PORT=9200
ELASTIC_TRANSPORT_PORT=9300

ES_JAVA_OPTS=-Xms1g -Xmx1g

# Linux absolute bind mount example:
ELASTIC_DATA_PATH=/opt/sherpaticket/elasticsearch/data

# Windows example:
# ELASTIC_DATA_PATH=./elastic_sherpaticket
```

Important for passwords containing `$`:

In Docker Compose `.env` files, escape dollar signs by doubling them.

Example:

```env
ELASTIC_PASSWORD=my$$Strong$$Password
```

The actual password will be:

```text
my$Strong$Password
```

---

## 3. Elasticsearch `docker-compose.yml`

```
Copy the elastic search folder to the location you want to have elastic search data. That location will be volume mounted.
```

## 5. Start Elasticsearch

Windows or Linux without init container:

```bash
docker compose up -d
```

Check logs:

```bash
docker logs -f sherpaticket_elasticsearch
```

Test Elasticsearch:

```bash
curl -u elastic:yourStrongElasticPasswordHere http://localhost:9200
```

PowerShell:

```powershell
curl.exe -u 'elastic:yourStrongElasticPasswordHere' http://localhost:9200
```

---

## 6. Kibana `.env`

Create this `.env` file in the Kibana Compose directory.

```env
KIBANA_VERSION=9.4.0
KIBANA_CONTAINER_NAME=sherpaticket_kibana

KIBANA_PORT=5601

ELASTICSEARCH_HOSTS=http://sherpaticket_elasticsearch:9200

KIBANA_ELASTICSEARCH_USERNAME=kibana_system
KIBANA_ELASTICSEARCH_PASSWORD=yourStrongKibanaSystemPasswordHere
```

Important for passwords containing `$`:

```env
KIBANA_ELASTICSEARCH_PASSWORD=my$$Kibana$$Password
```

The actual password will be:

```text
my$Kibana$Password
```

---

## 7. Kibana `docker-compose.yml`

```
Copy the kibana folder to the location you want to have kibana. You will need to run docker compose at that location.
```

---

## 8. Give Kibana permission to connect to Elasticsearch

Kibana should connect to Elasticsearch using the built-in `kibana_system` user.

Before starting Kibana, set the password for the `kibana_system` user in Elasticsearch.

### Linux Bash

Replace the passwords with your actual values.

```bash
curl -u elastic:yourStrongElasticPasswordHere \
  -X POST "http://localhost:9200/_security/user/kibana_system/_password" \
  -H "Content-Type: application/json" \
  -d '{"password":"yourStrongKibanaSystemPasswordHere"}'
```

### Windows PowerShell

```powershell
curl.exe -u 'elastic:yourStrongElasticPasswordHere' `
  -X POST "http://localhost:9200/_security/user/kibana_system/_password" `
  -H "Content-Type: application/json" `
  -d '{\"password\":\"yourStrongKibanaSystemPasswordHere\"}'
```

The password used in this command must match:

```env
KIBANA_ELASTICSEARCH_PASSWORD=yourStrongKibanaSystemPasswordHere
```

Do not log into the Kibana UI as `kibana_system`. That user is only for Kibana's internal connection to Elasticsearch.

Use the `elastic` user to log into the Kibana web UI:

```text
Username: elastic
Password: yourStrongElasticPasswordHere
```

---

## 9. Start Kibana

From the Kibana Compose directory:

```bash
docker compose up -d
```

Check logs:

```bash
docker logs -f sherpaticket_kibana
```

Open Kibana:

```text
http://localhost:5601
```

---


## 10. Restart commands

Restart Elasticsearch:

```bash
docker restart sherpaticket_elasticsearch
```

Restart Kibana:

```bash
docker restart sherpaticket_kibana
```

Restart both from their Compose directories:

```bash
docker compose down
docker compose up -d
```

---

## 11. Stop containers

Elasticsearch Compose directory:

```bash
docker compose down
```

Kibana Compose directory:

```bash
docker compose down
```

---

## 12. Delete Elasticsearch bind-mounted data

Only run this if you intentionally want to delete Elasticsearch data.

Example Linux path:

```bash
sudo rm -rf /opt/sherpaticket/elasticsearch/data
```

If using a relative path:

```bash
sudo rm -rf ./elastic_sherpaticket
```

---

## 13. Troubleshooting

### Elasticsearch cannot write to the data directory

Run:

```bash
sudo mkdir -p /opt/sherpaticket/elasticsearch/data
sudo chown -R 1000:0 /opt/sherpaticket/elasticsearch/data
sudo chmod -R g+rwX /opt/sherpaticket/elasticsearch/data
```

Then restart:

```bash
docker compose down
docker compose up -d
```

### Elasticsearch fails with `vm.max_map_count` error

Run:

```bash
sudo sysctl -w vm.max_map_count=262144
```

Permanent fix:

```bash
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

### Kibana cannot connect to Elasticsearch

Verify both containers are on the same network:

```bash
docker network inspect sherpaticket_net
```

Verify Elasticsearch is reachable:

```bash
curl -u elastic:yourStrongElasticPasswordHere http://localhost:9200
```

Verify the `kibana_system` password was set:

```bash
curl -u elastic:yourStrongElasticPasswordHere \
  -X POST "http://localhost:9200/_security/user/kibana_system/_password" \
  -H "Content-Type: application/json" \
  -d '{"password":"yourStrongKibanaSystemPasswordHere"}'
```

Then restart Kibana:

```bash
docker restart sherpaticket_kibana
```

# RABBITMQ

## Just copy the rabitmq folder and run docker compose. Make sure to copye the .env file and update the passwords.
