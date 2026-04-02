# Centralized Logging with ELK Stack

## Project Overview

This project implements a **Centralized Logging System** using the ELK Stack (Elasticsearch, Logstash, Kibana) on AWS. It collects logs from applications and system sources, processes them, and visualizes them in real-time dashboards.

---

## Architecture

- Filebeat collects logs
- Logstash processes logs
- Elasticsearch stores logs
- Kibana visualizes logs

---

## Technologies Used

###  ELK Stack
- Elasticsearch – Log storage & search engine  
- Logstash – Log processing pipeline  
- Kibana – Data visualization & dashboards  

### Supporting Tools
- Filebeat – Log shipping agent  
- Docker – Containerization (optional setup)  
- Java & Flask – Log generating applications  
- Linux – Deployment environment  
- AWS EC2 – Cloud infrastructure  

---

## Setup Instructions

###  Install Elasticsearch

```
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
sudo nano /etc/yum.repos.d/elastic.repo
```
```
[elastic-8.x]
name=Elastic repository for 8.x packages
baseurl=https://artifacts.elastic.co/packages/8.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```
```bash
sudo systemctl daemon-reexec
sudo yum install elasticsearch -y
sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch
```

### Install Logstash
```
sudo yum install logstash -y
```

# Create config:
```
sudo nano /etc/logstash/conf.d/logstash.conf
```

```
input {
  beats {
    port => 5044
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "logs-%{+YYYY.MM.dd}"
  }
  stdout { codec => rubydebug }
}
```
# Start:
```
sudo systemctl enable logstash
sudo systemctl start logstash
```

# Install Kibana
```
sudo yum install kibana -y
```
# Configure:
```
sudo nano /etc/kibana/kibana.yml
```
```
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://localhost:9200"]
```
# Start:
```
sudo systemctl enable kibana
sudo systemctl start kibana
```
# Access:
```
http://<EC2-PUBLIC-IP>:5601
```
### Install Filebeat

```
sudo yum install filebeat -y
```
# Configure:
```
sudo nano /etc/filebeat/filebeat.yml
```
```
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/*.log

output.logstash:
  hosts: ["localhost:5044"]
```
# Start:
```
sudo systemctl enable filebeat
sudo systemctl start filebeat
```

# Testing the Pipeline #
# Generate logs
```
echo "ELK TEST LOG" >> /var/log/test.log
```
# Check indices:
```
curl localhost:9200/_cat/indices?v
```

### Kibana Setup: 
1. Open Kibana
2. Go to Stack Management → Index Patterns
3. Create index pattern:
```
logs-*
```
