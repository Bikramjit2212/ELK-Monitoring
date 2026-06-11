# 🚀 ELK Stack Monitoring with Python Logging

A lightweight log aggregation and monitoring project demonstrating how to centralize application logs using the **ELK Stack (Elasticsearch, Logstash, and Kibana)** with a custom Python application.

This project showcases the fundamentals of log collection, parsing, indexing, and visualization—skills commonly used in DevOps, Site Reliability Engineering (SRE), and Cloud Operations.

---

## 📌 Project Overview

The application generates structured JSON logs from a Python script and sends them directly to Logstash over TCP.

Logstash receives and parses the logs before forwarding them to Elasticsearch for storage and indexing.

Kibana can then be used to search, analyze, and visualize the collected logs.

### Flow Architecture

```
Python Application
        │
        ▼
Custom Logging Handler
        │
TCP (Port 5044)
        │
        ▼
Logstash
        │
        ▼
Elasticsearch
        │
        ▼
Kibana Dashboard
```

---

## 🏗️ Project Structure

```
ELK-Monitoring-main/
├── app.py
├── docker-compose.yml
└── logstash.conf
```

---

## ⚙️ Technologies Used

* Python
* Docker
* Docker Compose
* Elasticsearch
* Logstash
* Kibana
* TCP Socket Programming
* JSON Logging

---

## ✨ Features

* Centralized logging architecture
* Custom Python logging handler
* JSON-formatted structured logs
* TCP-based log forwarding
* Logstash ingestion pipeline
* Elasticsearch indexing
* Kibana integration for visualization
* Containerized ELK deployment
* Demonstrates real-world monitoring concepts

---

## 🔍 Code Audit

### 1. Python Application (`app.py`)

The Python script acts as a sample application generating logs.

#### Logging Configuration

```python
logger = logging.getLogger("python-app")
logger.setLevel(logging.INFO)
```

Creates a dedicated logger named:

```
python-app
```

with INFO severity level.

---

### Custom Logstash Handler

```python
class LogstashHandler(logging.Handler):
```

A custom logging handler extends Python's built-in logging framework.

Responsibilities:

* Open TCP socket connections.
* Connect to Logstash.
* Send logs over the network.
* Close the connection safely.

---

### TCP Communication

```python
s.connect((HOST,PORT))
s.sendall((log_entry+"\n").encode("utf-8"))
```

Logs are transmitted directly to Logstash using:

* Host: `localhost`
* Port: `5044`
* Protocol: TCP

---

### Structured JSON Logs

Formatter:

```python
formatter = logging.Formatter(
    '{"message":"%(message)s","level":"%(levelname)s"}'
)
```

Sample generated log:

```json
{
    "message": "Hello from Python Log #3",
    "level": "INFO"
}
```

Structured logging enables efficient searching and filtering inside Elasticsearch.

---

### Dummy Log Generation

```python
for i in range(10):
    logger.info(f"Hello from Python Log #{i}")
    time.sleep(2)
```

Behavior:

* Generates 10 logs.
* Produces one log every 2 seconds.
* Useful for testing ingestion pipelines.

---

## 🐳 Docker Compose Analysis

The entire ELK stack is deployed using Docker Compose.

---

### Elasticsearch

Purpose:

* Stores indexed logs.
* Provides search capabilities.

Configuration:

```yaml
image: docker.elastic.co/elasticsearch/elasticsearch:9.2.3
```

Exposed Port:

```yaml
9200:9200
```

Single-node mode:

```yaml
discovery.type=single-node
```

Java Heap Configuration:

```yaml
ES_JAVA_OPTS=-Xm512m -Xm512m
```

> Note:
> This appears to be a typo and should ideally be:

```yaml
ES_JAVA_OPTS=-Xms512m -Xmx512m
```

for proper heap initialization.

---

### Logstash

Purpose:

* Receives logs.
* Parses incoming events.
* Forwards them to Elasticsearch.

Image:

```yaml
docker.elastic.co/logstash/logstash:9.2.3
```

Port:

```yaml
5044:5044
```

Pipeline mounted using:

```yaml
./logstash.conf
```

Dependency:

```yaml
depends_on:
  - elasticsearch
```

---

### Kibana

Purpose:

* Visualization layer.
* Log exploration.
* Dashboard creation.

Port:

```yaml
5601:5601
```

Accessible through:

```
http://localhost:5601
```

---

## 🔧 Logstash Pipeline Analysis

### Input Stage

```ruby
input {
    tcp {
        port => 5044
        codec => json_lines
    }
}
```

Responsibilities:

* Listen on TCP port 5044.
* Parse newline-delimited JSON logs.

---

### Filter Stage

```ruby
filter {}
```

Currently empty.

This section can later be extended for:

* Field enrichment
* Data transformation
* GeoIP lookups
* Grok parsing
* Conditional processing

---

### Output Stage

```ruby
elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    index => "python-logs-%{+YYYY.MM.dd}"
}
```

Stores logs into daily indices.

Example:

```
python-logs-2026.06.11
```

---

### Debug Output

```ruby
stdout {
    codec => rubydebug
}
```

Useful during development to inspect processed events.

---

## 🚀 Getting Started

### Prerequisites

Install:

* Docker
* Docker Compose
* Python 3.x

---

### Clone Repository

```bash
git clone <repository-url>
cd ELK-Monitoring-main
```

---

### Start ELK Stack

```bash
docker compose up -d
```

Verify containers:

```bash
docker ps
```

---

### Run Python Application

Open another terminal:

```bash
python app.py
```

---

## 📊 Viewing Logs in Kibana

Open:

```
http://localhost:5601
```

Steps:

1. Navigate to **Stack Management**.
2. Create a Data View.
3. Use index pattern:

```text
python-logs-*
```

4. Open **Discover**.
5. Search and analyze logs.

---

## 📈 DevOps Skills Demonstrated

* Centralized Logging
* ELK Stack Deployment
* Docker Compose Orchestration
* Structured JSON Logging
* TCP Networking
* Log Aggregation
* Elasticsearch Indexing
* Kibana Visualization
* Troubleshooting and Monitoring Fundamentals
* Container-Based Operations

---

## 🔮 Future Enhancements

Potential improvements include:

* Add Filebeat for log shipping.
* Secure ELK using authentication and TLS.
* Build Kibana dashboards.
* Integrate Prometheus and Grafana.
* Deploy ELK on Kubernetes.
* Add alerting using ElastAlert.
* Use Docker volumes for persistence.
* Implement CI/CD pipelines for deployment.

---

# 👨‍💻 Author

**Bikramjit Roy**

DevOps & Cloud Engineering Enthusiast passionate about automation, CI/CD, cloud-native practices, and building reliable software delivery pipelines.

GitHub:
https://github.com/Bikramjit2212

---

## ⭐ If you found this project useful, consider giving it a star.
