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
   
 <span style="color:red"> You must replace the value </span>
