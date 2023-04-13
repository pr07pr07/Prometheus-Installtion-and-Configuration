## Prometheus Installtion :

- Follow below steps to download Prometheus and start its process:

  ```bash
  wget https://github.com/prometheus/prometheus/releases/download/v2.40.1/prometheus-2.40.1.linux-amd64.tar.gz
  tar xvf prometheus-2.40.1.linux-amd64.tar.gz
  cd prometheus-2.40.1.linux-amd64
  ./prometheus > /dev/null 2>&1 &
  ```

- Add Prometheus user as below

  ```bash
  useradd --no-create-home --shell /bin/false prometheus
  ```
- Create Directories for storing prometheus config file and data

  ```bash
  sudo mkdir /etc/prometheus
  sudo mkdir /var/lib/prometheus
  ```
- Change the permissions

  ```bash
  sudo chown prometheus:prometheus /etc/prometheus
  sudo chown prometheus:prometheus /var/lib/prometheus
  ```
- Copy the binaries

  ```bash
  sudo cp /home/opc/prometheus-2.40.1.linux-amd64/prometheus /usr/local/bin/
  sudo cp /home/opc/prometheus-2.40.1.linux-amd64/promtool /usr/local/bin/
  ```
  





