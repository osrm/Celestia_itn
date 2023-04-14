This repository is created for celestia itn task(https://docs.celestia.org/nodes/itn-toolings/). This is to build a tool to help with node operations, and light-node was used as an example.

------------------------------------------------------
Create toolings for the Celestia Network
===================
Create node monitoring tools that can be easily deployed using grafana as one of the useful tools to be used in celestia network.

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
