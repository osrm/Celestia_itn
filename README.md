This repository is created for celestia itn task(https://docs.celestia.org/nodes/itn-toolings/). This is to build a tool to help with node operations, and light-node was used as an example.

------------------------------------------------------
Create analytics tool for the Celestia Network node
===================

Today, many vps providers support graphical analysis tools for servers.

However, it is also difficult to set alarms for certain behaviors because operators who do not use the vps provider cannot take advantage of them and cannot customize analysis tools to analyze the desired areas.

Creating a node monitoring tool that can be easily deployed using grafana and promethus can be useful for monitoring nodes on Celestia network.

### Pre-emptive conditions
- Installed Celestia light-node 
- Grafana account & API

If pre-emptive conditions are not met, please naviagate https://docs.celestia.org/nodes/light-node/ to set-up the node and visit https://grafana.com/products/cloud/ to get grafana account & API.


Now let's install grafana & prometheus to graphically monitor the nodes.

---------------------------------------------
### Install Grafana    
   
Install and run node_exporter on the node

```
wget https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz
```

Extract the node_exporter binary

```
tar xvfz node_exporter-1.5.0.linux-amd64.tar.gz
```

Change to the directory created during extraction

```
cd node_exporter-1.5.0.linux-amd64/
```

Make the node_exporter binary executable

```
chmod +x node_exporter
```

Run the node_exporter binary

```
./node_exporter
```

Test that metrics are being exported on port 9100

```
curl http://localhost:9100/metrics
```
 
If you see metrics on your screen, all is well. If not, check your steps for typos, make sure the binary is executable, and whether curl works with other URLs.
 
If you don’t want to start node_exporter directly from the command line, you can create a systemd service for it, similar to Creating a systemd service to manage the agent.(https://grafana.com/docs/grafana-cloud/data-configuration/agent/agent_as_service/)
 
Next, we will download and install Prometheus on the node to scrape the metrics being provided by node_exporter and send them to Grafana Cloud.

----------------------
### Install Prometheus

Download the Prometheus compressed package

```
wget https://github.com/prometheus/prometheus/releases/download/v2.43.0/prometheus-2.43.0.linux-amd64.tar.gz
```

Extract the binary

```
tar xvf prometheus-2.43.0.linux-amd64.tar.gz
```

Change to the directory created during extraction

```
cd prometheus-2.43.0.linux-amd64
```

Create a configuration file for Prometheus, so that it can scrape the metrics and to send them to Grafana Cloud. This configuration file has many options. For our example, it only needs three sections:

 

- `global` is the section into which configurations common across all Prometheus actions are placed. In this example, we set the scrape_interval for checking and grabbing metrics from configured jobs to happen every 15 seconds.
- `scrape_configs` is where we name our job; this name will be used in Grafana to help you find associated metrics. It is also where we configure Prometheus to find the metrics for that job.
- `remote_write` is where we instruct Prometheus to send the scraped metrics to a secondary endpoint.
Edit this file to include your Grafana Cloud username and the API key you created earlier.
   
To confirm your username and URL, first navigate to the Cloud Portal, then from the Prometheus box, click Send Metrics.
   
![image](https://user-images.githubusercontent.com/90407222/232095130-9ef598a6-4577-455c-9550-ed92cae08bb4.png)
    
Create a Prometheus configuration file named "prometheus.yml" in the same directory as the Prometheus binary with the following content.
   
You must replace the value!

```
global:
  scrape_interval: 60s

scrape_configs:
  - job_name: node
    static_configs:
      - targets: ['localhost:9100']

remote_write:
  - url: '<Your Metrics instance remote_write endpoint>'
    basic_auth:
      username: 'your grafana username'
      password: 'your Grafana API key'
   ```
   
   Run prometheus
   
```
./prometheus --config.file=./prometheus.yml
```

---------------
### Check that metrics are being ingested into Grafana Cloud

Within minutes, metrics should begin to be available in Grafana Cloud. To test this, use the Explore feature. Click Explore in the sidebar to start. This takes you to the Explore page, which looks like this.
   
![image](https://user-images.githubusercontent.com/90407222/232097987-8818d774-cf26-4fe2-a396-6e2596d958f2.png)
   
At the top of the page, use the dropdown menu to select your Prometheus data source.

Use the Metrics dropdown to find the entry for node, which is the ```job_name``` we created in ```prometheus.yml```.
   
![image](https://user-images.githubusercontent.com/90407222/232098131-1e6176ba-14b3-40c1-8b50-088166dd39a3.png)
   
If node is not listed, metrics are not being collected. If metrics are listed, this confirms that metrics are being received.

If metrics are not displayed after several minutes, check your steps for typos, make sure the binary is executable, and whether Prometheus is running on the Linux machine.

---
### Import a dashboard

Official and community-built dashboards are listed on the Grafana website Dashboards page.(https://grafana.com/grafana/dashboards/)

Dashboards on this page will include information in the Overview tab about special configurations you may need to use to get the dashboard to work. For our example, we require a dashboard that is built to display Linux Node metrics using Prometheus and node_exporter, so we chose Linux Hosts Metrics | Base.(https://grafana.com/grafana/dashboards/10180)
   
Note the ID of the dashboard: 10180. We will use this ID in the next step.

1. In Grafana, click Dashboards in the left-side menu to go to the Dashboards page.

2. Click New and select Import in the dropdown.

3. Enter the ID number of the dashboard we selected.

4. Click Load.
   
You’ll get a dashboard like this.
   
![image](https://user-images.githubusercontent.com/90407222/232098865-cfa2b484-f73a-443a-9916-b419c33ac7cc.png)
