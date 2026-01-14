# $${\color{red} \textbf{Elasticsearch Logstash Kibana}}$$

ELK stack is the powerfool toolset used for searching, analyzing, and visualizing log data in real time.

## $${\color{green} \textbf{🔍 1. Elasticsearch}}$$

A distributed search and analytics engine.

Stores data and enables fast full-text search, filtering, and aggregations.

Backend of the ELK Stack.

## $${\color{green} \textbf{📦 2. Logstash}}$$

A data processing pipeline.

Ingests data from multiple sources, transforms it, and then sends it to a destination (usually Elasticsearch).

Supports complex parsing, filtering, and enrichment of logs.

## $${\color{green} \textbf{📊 3. Kibana}}$$

A visualization and dashboard tool.

Interfaces directly with Elasticsearch to help you explore and visualize data.

Useful for creating dashboards, monitoring logs, or detecting anomalies.



# $${\color{purple} \textbf{🔄 Workflow Summary:}}$$

**Logstash** (or Beats) collects logs from your servers or applications.

Logs are processed and sent to **Elasticsearch**.

You use **Kibana** to visualize, search, and monitor the data.



# $${\color{red} \textbf{ELK SETUP}}$$

REFER : https://www.digitalocean.com/community/tutorials/how-to-install-elasticsearch-logstash-and-kibana-elastic-stack-on-ubuntu-22-04

## Install Elasticsearch
```
apt install default-jdk
```
```
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch |sudo gpg --dearmor -o /usr/share/keyrings/elastic.gpg
echo "deb [signed-by=/usr/share/keyrings/elastic.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
```
```
sudo apt update
sudo apt install elasticsearch
```
```
sudo nano /etc/elasticsearch/elasticsearch.yml
```
<img width="478" height="95" alt="image" src="https://github.com/user-attachments/assets/cb78a296-b189-4bb6-a0c0-2b85f84620ce" />

```
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
```
```
curl -X GET "localhost:9200"
```

## Install Kibana
```
sudo apt install kibana
```
```
sudo systemctl enable kibana
sudo systemctl start kibana
```
`kibana.yml`

<img width="672" height="147" alt="image" src="https://github.com/user-attachments/assets/808b1bb7-af37-426e-9782-9df608ef84e5" />

<img width="506" height="143" alt="image" src="https://github.com/user-attachments/assets/19c58c7f-4f98-42fc-9085-bd37f2dc1022" />

**Host kibana <public-ip:5601>**

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/c88036a8-8dd6-49c9-a439-225a92f716c8" />

## Install Logstash 

```
sudo apt update
sudo apt install logstash
```
**Create a Logstash Pipeline**

```
sudo nano /etc/logstash/conf.d/simple-pipeline.conf
```
```
input {
  file {
    path => "/var/log/test-log.log"
    start_position => "beginning"
    sincedb_path => "/dev/null"
  }
}

filter {
  grok {
    match => { "message" => "%{TIMESTAMP_ISO8601:timestamp} \[%{LOGLEVEL:level}\] %{GREEDYDATA:msg}" }
  }
  date {
    match => ["timestamp", "ISO8601"]
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "logstash-demo"
  }
  stdout { codec => rubydebug }
}
```
```
echo "2025-09-10T12:34:56 [INFO] This is a test log line" | sudo tee /var/log/test-log.log
```
```
sudo systemctl start logstash
```
Now we can see the logs on kibana with index pattern creation. 

