# Install-Prometheus-and-Grafana

## What is Prometheus?
Prometheus monitoring solution is a free and open-source solution for monitoring metrics, events, and alerts. It collects and records metrics from servers, containers, and applications. In addition to providing a flexible query language (PromQL), and powerful visualization tools, it also provides an alerting mechanism that sends notifications when needed.

## Architecture
![Prometheus architecture](https://prometheus.io/assets/architecture.png)

## How to install Prometheus
### Option 1: Running Prometheus as a binary
#### Step 1. Download the libray
Vist Prometheus downloads page(https://prometheus.io/download/) and pick the library which is matches yours OS.
```
tar -xvf prometheus-xxx.xxx-amd64.tar.gz
```

#### Step 2. Create a System User for Prometheus
```
sudo groupadd prometheus
sudo useradd -g prometheus prometheus
```

#### Step 3. Create Directories for Prometheus
```
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```
#### Step 4. Move the Prometheus Files & Set Owner
```
cd prometheus-xxx.xxx-amd64
sudo mv ./prometheus /usr/local/bin
sudo mv ./promtool /usr/local/bin
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```
#### Step 5.  Move the Configuration Files & Set Owner
```
sudo mv consoles /etc/prometheus
sudo mv console_libraries /etc/prometheus
sudo mv prometheus.yml /etc/prometheus

sudo chown prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
sudo chown -R prometheus:prometheus /var/lib/prometheus
```
#### Step 6.  Edit main Prometheus configuration file
```
vi /etc/prometheus/prometheus.yml

global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]

```
#### Step 7.  Create Prometheus Systemd Service
create prometheus.service
```
vi /etc/systemd/system/prometheus.service
```
```bash
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```
#### Step 8.  Enable Prometheus Systemd Service
```
sudo systemctl enable prometheus
sudo systemctl start prometheus
```
#### Step 9.  Check Prometheus Systemd Service
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/ded631b9-01c8-4a25-b962-a899b62d59ba)

#### Step 10.  Open the browser
URL: http://localhost:9090
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/1c74b84d-ea28-4c03-8b6b-4baec1d185e2)

