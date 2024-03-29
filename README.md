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
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/44c71aa0-838d-46b1-b6ae-42a98c469f4d)
#### Step 10.  Open the browser
URL: http://localhost:9090
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/1c74b84d-ea28-4c03-8b6b-4baec1d185e2)

## What is Grafana?
Query, visualize, alert on, and understand your data no matter where it’s stored. With Grafana you can create, explore, and share all of your data through beautiful, flexible dashboards.

## How to install Grafana
Install in Ubuntu
#### Step 1.  Install Grafana
```
wget https://dl.grafana.com/oss/release/grafana_7.1.3_amd64.deb  
sudo dpkg -i grafana_7.1.3_amd64.deb
sudo service grafana-server start
sudo service grafana-server status
sudo systemctl enable grafana-server.service
```
#### Step 2.  Open the browser
URL: http://localhost:3000
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/a5b132a4-94b0-4320-bcd4-f01ab2f49ef9)

default 
username: admin
password: admin

#### Step 3.  Create Prometheus Dashboard 
先建置 Data source for prometheus
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/769e18cc-708e-494f-bc89-ff81a2bc01d5)
選取 Prometheus
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/763b303f-b469-47c0-9185-5cb38969d9af)
設定 Prometheus 路徑
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/c1873fcf-d3a8-45bf-9c43-3ae57d5d487b)
設定完成儲存成功後會出現綠色框框
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/5076460c-6287-4f99-8cc8-64977a9742f1)
新增 Dashboard
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/593fa5ba-c063-4903-95dd-f29261f2acfe)
設定需要看的參數，完成後按下右上方的 Apply
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/28ffa6f3-8e3d-472f-9cb3-2f8f0cf2252a)
Dashboard 顯示 Prometheus 就完成了
![image](https://github.com/joychang12/Install-Prometheus-and-Grafana/assets/108848733/5c297a63-1eb1-47d3-ba07-167476cc102a)
