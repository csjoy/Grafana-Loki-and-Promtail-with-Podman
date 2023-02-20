# Grafana-Loki-and-Promtail-with-Podman


Grafana Loki + Promtail is a common configuration for enabling observability with logs. The following is a setup guide with podman.

## Inspiration
Official setup guide is not updated yet for latest release and the instruction doesn't work with podman. Podman uses pod to group containers. Which is modern and easier way to manage linked containers.

### Step 1
Download the latest configuration files using the links below or clone this repo.
```
wget https://raw.githubusercontent.com/grafana/loki/v2.7.3/cmd/loki/loki-local-config.yaml -O loki-config.yaml
wget https://raw.githubusercontent.com/grafana/loki/v2.7.3/clients/cmd/promtail/promtail-docker-config.yaml -O promtail-config.yaml
```

### Step 2
Pull all the required docker images
```
podman pull grafana/grafana
podman pull grafana/loki
podman pull grafana/promtail
```
### Step 3
Create a pod to keep loki and promtail container in the same network. Also expose the port 3100 to access metrices from Loki.
```
podman pod create --name LokiPromtail -p 3100:3100
```
### Step 4
From the directory where the yaml files are located, run the following commands
```
podman run --pod LokiPromtail -d --name loki -v ./loki-config.yaml:/etc/loki/local-config.yaml grafana/loki
podman run --pod LokiPromtail -d --name promtail -v ./promtail-config.yaml:/etc/promtail/docker-config.yaml -v /var/log:/var/log grafana/promtail
```
### Step 5
You can see log aggregation and visualization data by visiting localhost:3100/metrics. Now run and visit grafana webui with default credentials and setup Loki datasource to {your local ip}:3100
```
podman run -d --name grafana -p 3000:3000 grafana/grafana
```
<img src="https://raw.githubusercontent.com/csjoy/Images/master/grafana_loki.png" />
