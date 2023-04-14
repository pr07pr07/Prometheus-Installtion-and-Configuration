# Prometheus Installtion and configurations

## Prometheus Installtion systemd

Now, let's setup Prometheus and create a systemd service unit file to manage the Prometheus service by doing the steps below


- Download Prometheus binaries from [here](https://prometheus.io/download/#prometheus)
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


- Download Node Exporter binaries from [here](https://prometheus.io/download/#node_exporter)
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
  sudo cp /home/opc/node_exporter-1.5.0.linux-amd64/node_exporter /usr/local/bin/
  ```
- Change the ownership of binaries:
  ```bash
  sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
  ```
- Create a service for **node_exporter**:
  ```bash
  sudo vi /etc/systemd/system/node_exporter.service
  ```
- Add below lines in it:
  ```lines
  [Unit]
  Description=Node Exporter
  Wants=network-online.target
  After=network-online.target

  [Service]
  User=node_exporter
  Group=node_exporter
  Type=simple
  ExecStart=/usr/local/bin/node_exporter

  [Install]
  WantedBy=multi-user.target
  ```
- Start and Enable the service:
  ```bash 
  sudo systemctl daemon-reload
  sudo systemctl start node_exporter
  sudo systemctl enable node_exporter
  sudo systemctl status node_exporter
  ```
## Prometheus Configuration
![image](https://user-images.githubusercontent.com/67961663/231862578-287c58d0-368c-4e6a-b506-a63a0f74842e.png)

- Update or Modify the **/etc/prometheus/prometheus.yml** file to add a job called nodes to start scraping the one or even more node_exporters.
  ```bash
  vi /etc/prometheus/prometheus.yml
  ```
- Add below code under scrape_configs:
  ```lines 
   - job_name: "nodes"
     static_configs:
        - targets: ['node01:9100', 'node02:9100']
  ```
 - Make sure to restart the Prometheus service by using below command after changes if we are using static config changes.
   ```bash
   systemctl restart prometheus
   ```
   
## Authentication/Encryption
![image](https://user-images.githubusercontent.com/67961663/231985459-2c17fb36-0c4c-4f18-ab28-04b5ef8dd22e.png)

- SSH to node_exporter nodes:
- Create the config:
  ```bash
  mkdir /etc/node_exporter/
  touch /etc/node_exporter/config.yml
  chmod 700 /etc/node_exporter
  chmod 600 /etc/node_exporter/config.yml
  chown -R nodeusr:nodeusr /etc/node_exporter
  ```
- Edit **node_exporter** service
  ```bash
  vi /etc/systemd/system/node_exporter.service
  ```
- Change below line:
  ```bash
  From
  ExecStart=/usr/local/bin/node_exporter
  To 
  ExecStart=/usr/local/bin/node_exporter --web.config=/etc/node_exporter/config.yml
  ```
- Reload the daemon and Restart node_exporter service
  ```
  systemctl daemon-reload
  systemctl restart node_exporter
  exit
  ```
- Follow same steps for other nodes if any

## Configure Prometheus and Node servers to use authentication to communicate

- SSH to **node_exporter** nodes
- Install **apache2-utils** package
  ```bash
  apt update
  apt install apache2-utils -y
  ```
- Generate password hash:
  ```bash
  htpasswd -nBC 10 "" | tr -d ':\n'; echo
  ```
- It will ask for the password twice as below (enter password **secret-password** twice)

- Finally, you will get a hashed value of your password.
- Edit /etc/node_exporter/config.yml file.
  ```bash
  vi /etc/node_exporter/config.yml
  ```
- Add below lines in it:
  ```bash
  basic_auth_users:
  prometheus: <hashed-password>
  ```
- Restart **node_exporter** service
  ```bash
  systemctl restart node_exporter
  ```
- You can verify the changes using curl command:
  ```bash
  curl http://node01:9100/metrics
  ```
- return output should be **Unauthorized**

- Note: Follow same steps for other nodes except generating the password hash, you should be able to use the same password hash for other nodes also.

- Are you able to access the metrics using correct credentials now? Try using below given commands:
  ```bash
  curl -u prometheus:secret-password http://hostname:9100/metrics
  curl -u prometheus:secret-password http://hostname:9100/metrics
  ```
- Now, let's configure the **Prometheus** server to use **authentication** when scraping **metrics** from **node** servers.

- Edit the Prometheus configuration file
  ```bash
  vi /etc/prometheus/prometheus.yml
  ```
- Under **- job_name: "nodes"** add below lines:
  ```bash
  basic_auth:
      username: prometheus
      password: secret-passwor
  ```
- Restart **prometheus** service:
  ```bash
  systemctl restart prometheus
  ```




  











