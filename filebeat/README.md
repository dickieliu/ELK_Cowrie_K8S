
# ELK with cowrie on Kubernetes Tutorial

This repository provides a setup for running an ELK stack (Elasticsearch, Logstash, and Kibana) along with Filebeat and Cowrie on a Kubernetes cluster. Filebeat is used to forward Cowrie's logs to Elasticsearch for centralized logging and analysis.

## Overview

- **Cowrie**: A medium interaction SSH and Telnet honeypot that logs brute force attacks and exploits.
- **Filebeat**: A lightweight shipper for forwarding and centralizing log data. In this setup, Filebeat sends logs from Cowrie to Elasticsearch.
- **Elasticsearch**: A distributed search and analytics engine, storing the logs forwarded by Filebeat.
- **Kibana**: A visualization tool for Elasticsearch, allowing you to explore and visualize the data.
- **Logstash**: Processes incoming logs before forwarding them to Elasticsearch.

## Architecture

The setup consists of the following components:

- **Cowrie**: A containerized version of Cowrie running in the `default` namespace.
- **Filebeat**: Runs as a DaemonSet in the `kube-system` namespace, collecting logs from Cowrie and sending them to Elasticsearch.
- **Elasticsearch**: Running in the `elk-stack` namespace, where it stores and indexes logs.
- **Kibana**: Provides a web interface for interacting with Elasticsearch, visualizing the data, and querying logs.

## Setup Instructions

### 1. Deploy Cowrie

Cowrie is deployed in the `default` namespace. To access Cowrie externally:

- Cowrie is exposed on port `32222`. You can access it externally via the IP of your Kubernetes node and port `32222` to attempt SSH or Telnet connections.
- Example: `ssh root@<node-ip> -p 32222`

### 2. Filebeat Configuration

Filebeat is configured to forward Cowrie logs to Elasticsearch.

- The Filebeat DaemonSet is configured to look for Cowrie logs located at `/var/log/containers/cowrie*.log`.
- It sends the logs to Elasticsearch at the specified endpoint (`elasticsearch:9200`), using the provided credentials.

### 3. Elasticsearch and Kibana

- Elasticsearch is running in the `elk-stack` namespace. You can query the logs directly from Elasticsearch or use Kibana for visualization.
- Kibana provides an interface to view the logs and set up dashboards. To view the logs, go to the Discover tab and select the `filebeat-*` index pattern.

## How to Attack Cowrie

To simulate an attack on Cowrie, you can connect to the Cowrie SSH/Telnet interface exposed externally on port `32222`.

- **SSH**: You can use a tool like `ssh` to connect to Cowrie.
  
  ```bash
  ssh root@<node-ip> -p 32222
  ```

- **Telnet**: Similarly, you can use `telnet` to connect.
  
  ```bash
  telnet <node-ip> 32222
  ```

Once connected, you can attempt brute force login attempts, and Cowrie will log the activity. These logs will be forwarded to Elasticsearch via Filebeat for further analysis in Kibana.

## View Logs in Kibana

1. Open Kibana in your browser.
2. Navigate to the **Discover** tab.
3. Select the `filebeat-*` index pattern to view the logs.
4. Analyze the logs for various attack attempts such as SSH brute force or failed login attempts.

## Troubleshooting

- If you don't see logs in Kibana, ensure that Filebeat is properly configured and running in your Kubernetes cluster.
- Check if the Filebeat pods are running:

  ```bash
  kubectl get pods -n kube-system -o wide
  ```

- If you're unable to access Cowrie externally, check if the `32222` port is exposed and accessible.

## File Structure

```plaintext
.
├── cowrie
│   ├── cowrie-service.yaml      # Service definition for Cowrie
│   └── cowrie-pod.yaml          # Cowrie pod definition
├── elasticsearch.yaml           # Elasticsearch deployment YAML
├── filebeat
│   ├── damonset.yaml            # Filebeat DaemonSet definition
│   ├── filebeat-configmap.yaml  # Filebeat config map
│   ├── rolebinding.yaml         # Role binding for Filebeat
│   ├── role.yaml                # Role definition for Filebeat
│   └── service-account.yaml     # Service account for Filebeat
├── images
│   └── logstash
│       └── Dockerfile           # Custom Logstash Dockerfile
├── kibana-configmap.yaml        # Kibana config map
├── kibana.yaml                  # Kibana deployment YAML
├── logstash-configmap.yaml      # Logstash config map
├── logstash.yaml                # Logstash deployment YAML
├── namespace.yml                # Namespace definition
└── README.md                    # Project README
```

