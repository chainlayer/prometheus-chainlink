# Guide to setup prometheus and grafana with your chainlink setup

## Install prerequisites
For this tutorial you need a working Prometheus and Grafana setup. Prometheus needs to run on a machine with sufficient disk (or at least the ability to increase it preferably online), once you're hooked to it you will want to add more metrics :)

Lets start with setting up grafana

### Grafana
Just follow the steps here for your distribution
https://grafana.com/grafana/download

After installation it should be available on http://[your ip]:3000/login

### Prometheus
You can just install Prometheus with apt or yum, this tutorial assumes ubuntu

```
apt install -y prometheus
```

This installs prometheus and enables and starts it with systemd. The UI of prometheus is available at http://[your ip]:9090 by default. The prometheus node exporter exports interesting information about the system running prometheus on http://[your ip]:9100/metrics. Its not needed for the rest of this tutorial so you could disable it if you want.

For both its advised to firewall the ports or change the listen address to local loopback in `/etc/default/prometheus` and `/etc/default/prometheus-node-exporter`.
  
```
ARGS="--web.listen-address=127.0.0.1:9090"
```

### Configure Prometheus
Now we need to tell Prometheus to scrape the metrics of the chainlink node. Chainlink exposes metrics on the /metrics endpoint of the ui, by default thats http://localhost:6688/metrics. One important thing to notice is that prometheus cannot scrape from https endpoints so if you configured https you will also need to open your http endpoint specifically for prometheus.

Telling prometheus to scrape something is done by editing the /etc/prometheus/prometheus.yml file and adding these lines:
```
# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.

  - job_name: 'chainlink_p1'
    static_configs:
    - targets: ['127.0.0.1:6688']
    metrics_path: '/metrics'
```
change the target accordingly and restart prometheus with `service prometheus restart`. If you run two nodes you can add them both with a different name (in this example chainlink_p1 and chainlink_p2). Mind that these names are used in the dashboard example as well.

### Configure grafana datasource
Now login to your grafana install with admin/admin and change your password. Click the add new datasource link and select prometheus. Enter `http://localhost:9090` as url and click `save & test`. 

![GitHub Logo](/images/screen1.png)
