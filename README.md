## Prometheus Installtion :

- Add Prometheus user as below

  ```bash
  useradd --no-create-home --shell /bin/false prometheus
  ```
- Create Directories for storing prometheus config file and data

  ```bash
  mkdir /etc/prometheus
  mkdir /var/lib/prometheus
  ```
- Change the permissions

  ```bash
  chown prometheus:prometheus /etc/prometheus
  chown prometheus:prometheus /var/lib/prometheus
  ```
- Copy the binaries

  ```bash
  cp /root/prometheus-2.40.1.linux-amd64/prometheus /usr/local/bin/
  cp /root/prometheus-2.40.1.linux-amd64/promtool /usr/local/bin/
  ```
  





