# Prometheus Installtion and configurations

## Prometheus Installtion systemd

Now, let's setup Prometheus and create a systemd service unit file to manage the Prometheus service by doing the steps below


- Download Prometheus binaries from 
  ```bash
  wget https://github.com/prometheus/prometheus/releases/download/v2.43.0/prometheus-2.43.0.linux-amd64.tar.gz
  tar xvf prometheus-2.43.0.linux-amd64.tar.gz
  ```
- Create Prometheus user as below:
  ```bash
  sudo useradd --no-create-home --shell /bin/false prometheus
  ```
- Create Directories for storing prometheus config file and data:
  ```bash
  sudo mkdir /etc/prometheus
  sudo mkdir /var/lib/prometheus
  ```
- Change the permissions:
  ```bash
  sudo chown prometheus:prometheus /etc/prometheus
  sudo chown prometheus:prometheus /var/lib/prometheus
  ```
- Copy the binaries:
  ```bash
  sudo cp /home/opc/prometheus-2.43.0.linux-amd64/prometheus /usr/local/bin/
  sudo cp /home/opc/prometheus-2.43.0.linux-amd64/promtool /usr/local/bin/
  ```
- Change the ownership of binaries:
  ```bash
  sudo chown prometheus:prometheus /usr/local/bin/prometheus
  sudo chown prometheus:prometheus /usr/local/bin/promtool
  ```
- Copy the directories **consoles** and **console_libraries**:
  ```bash
  sudo cp -r /home/opc/prometheus-2.43.0.linux-amd64/consoles /etc/prometheus
  sudo cp -r /home/opc/prometheus-2.43.0.linux-amd64/console_libraries /etc/prometheus
  ```
- Change the ownership of directories **consoles** and **console_libraries**:
  ```bash
  sudo chown -R prometheus:prometheus /etc/prometheus/consoles
  sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
  ```
- Move **prometheus.yml** file to /etc/prometheus directory:
  ```bash
  sudo cp /home/opc/prometheus-2.43.0.linux-amd64/prometheus.yml /etc/prometheus/prometheus.yml
  ```
- Change the ownership of file **/etc/prometheus/prometheus.yml**:
  ```bash
  sudo chown prometheus:prometheus /etc/prometheus/prometheus.yml
  ```
- Create a service for **prometheus**:
  ```bash
  sudo vi /etc/systemd/system/prometheus.service
  ```
- Add below lines in it:
  ```lines
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
- Start and Enable the service:
  ```bash 
  sudo systemctl daemon-reload
  sudo systemctl start prometheus
  sudo systemctl enable prometheus
  sudo systemctl status prometheus
  ```

## Node Exporter installation systemd
Now, let's setup Node Exporter and create a systemd service unit file to manage the Node Exporter service by doing the steps below


- Download Prometheus binaries from [URL](https://prometheus.io/download)
  ```bash
  wget https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz
  tar xvf node_exporter-1.5.0.linux-amd64.tar.gz
  ```
- Create  node_exporter user as below:
  ```bash
  sudo useradd --no-create-home --shell /bin/false node_exporter
  ```
- Copy the binaries:
  ```bash
  sudo cp /home/opc/node_exporter-1.5.0.linux-amd64/prometheus /usr/local/bin/node_exporter
  ```
- Change the ownership of binaries:
  ```bash
  sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
  ```


  

  





