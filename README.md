# Prometheus Installtion

Now, let's setup Prometheus and create a systemd service unit file to manage the Prometheus service by doing the steps below


- Download Prometheus from:
  ```bash
  wget https://github.com/prometheus/prometheus/releases/download/v2.43.0/prometheus-2.43.0.linux-amd64.tar.gz
  tar xvf prometheus-2.43.0.linux-amd64.tar.gz
  ```
- Add Prometheus user as below:
  ```bash
  useradd --no-create-home --shell /bin/false prometheus
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
  chown prometheus:prometheus /usr/local/bin/prometheus
  chown prometheus:prometheus /usr/local/bin/promtool
  ```
- Copy the directories **consoles** and **console_libraries**:
  ```bash
  cp -r /home/opc/prometheus-2.43.0.linux-amd64/consoles /etc/prometheus
  cp -r /home/opc/prometheus-2.43.0.linux-amd64/console_libraries /etc/prometheus
  ```
- Change the ownership of directories **consoles** and **console_libraries**:
  ```bash
  chown -R prometheus:prometheus /etc/prometheus/consoles
  chown -R prometheus:prometheus /etc/prometheus/console_libraries
  ```
- Move **prometheus.yml** file to /etc/prometheus directory:
  ```bash
  cp /home/opc/prometheus-2.43.0.linux-amd64/prometheus.yml /etc/prometheus/prometheus.yml
  ```
- Change the ownership of file **/etc/prometheus/prometheus.yml**:
  ```bash
  chown prometheus:prometheus /etc/prometheus/prometheus.yml
  ```
- Create a service for **prometheus**:
  ```bash
  vi /etc/systemd/system/prometheus.service
  ```
- Add below lines in it:
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
- Run below commands:
  ```
systemctl daemon-reload
systemctl start prometheus
systemctl enable prometheus
systemctl status prometheus
  ```
  





