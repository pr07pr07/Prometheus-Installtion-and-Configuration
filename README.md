# Prometheus Installtion :

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




