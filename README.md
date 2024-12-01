# ELK on K8S

Deploying the ELK stack in a Kubernetes environment with Cowrie and Filebeat for log collection and monitoring.

<br>

## Prerequisites

Before starting the setup, ensure the following prerequisites are met:

1. **Kubernetes Cluster**: Ensure you have a running Kubernetes cluster. This can be a local cluster using Minikube or a cloud-based cluster.
2. **kubectl**: The Kubernetes command-line tool should be configured to interact with your cluster.
3. **Docker**: Docker should be installed and accessible on the system.
4. **Helm (Optional)**: If you want to manage Kubernetes resources using Helm, ensure it's installed.
5. **Elastic Stack**: The ELK stack (Elasticsearch, Logstash, Kibana) should be installed and configured on the cluster.
6. **Cowrie**: A honeypot application running on the cluster to simulate attacks and generate logs.
7. **Filebeat**: The log shipper should be installed to send logs from Cowrie to Elasticsearch.
   **Flannel**  For network config in k8s.
<br>

## Configuration Steps

### Step 1: Create Namespace

First, create a Kubernetes namespace for all the resources.


### Step 2: Create Resources for ELK Stack

Apply the necessary configuration files for Elasticsearch, Logstash, and Kibana.

# !/bin/sh 
# create namespace
kubectl create -f ./namespace.yaml

# !/bin/sh
# apply ELK on k8s
kubectl apply -f ./logstash-configmap.yaml
kubectl apply -f ./kibana-configmap.yaml
kubectl apply -f ./elasticsearch.yaml
kubectl apply -f ./logstash.yaml
kubectl apply -f ./kibana.yaml


### Step 3: Create Filebeat Resources

Filebeat collects logs from the Cowrie pod and sends them to Elasticsearch.
# !/bin/sh
cd /filebeat
kubectl apply -f .


### Step 4: Create Cowrie Resources

Create the necessary resources to run the Cowrie honeypot.
# !/bin/sh
cd /cowrie
kubectl apply -f cowrie-service.yaml
kubectl apply -f cowrie-pod.yaml

<br>

## Verification

### Step 1: Check Elasticsearch Connection

You can verify if Elasticsearch is running by sending a request to the local Elasticsearch node.

#!/bin/sh curl -X GET "http://localhost:9200"


If Elasticsearch is running correctly, you should see a response similar to this:

{ "name" : "7709ec1b438b", "cluster_name" : "docker-cluster", "cluster_uuid" : "iPGC1QefSwK00RCqpRnmfQ", "version" : { "number" : "7.15.2", "build_flavor" : "default", "build_type" : "docker", "build_hash" : "93d5a7f6192e8a1a12e154a2b81bf6fa7309da0c", "build_date" : "2021-11-04T14:04:42.515624022Z", "build_snapshot" : false, "lucene_version" : "8.9.0", "minimum_wire_compatibility_version" : "6.8.0", "minimum_index_compatibility_version" : "6.0.0-beta1" }, "tagline" : "You Know, for Search" }


### Step 2: Check Kibana Access

Access Kibana at `http://clusterip:5601` in your browser to view and analyze the logs.

### Step 3: Cowrie Access

Cowrie can be accessed externally through port 32222. Use  (ssh) to simulate an attack and generate logs.

Example:

ssh <external-ip> -p 32222


Once you connect, perform an attack simulation to see Cowrie generate logs.

<br>

## ELK Stack Reference

* **Images**
  * Elasticsearch and Kibana: Using the default images provided by Elastic.
  * Logstash: Built a new image based on the base image with additional plugins. You can find it [here](https://hub.docker.com/repository/docker/sir0123/logstash).

<br>

* **Configuration References**
  * [Install Elasticsearch on Docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)
  * [Install Kibana on Docker](https://www.elastic.co/guide/en/kibana/current/docker.html)
  * [Run Logstash on Docker](https://www.elastic.co/guide/en/logstash/current/docker.html)
  * [Logstash Configuration Files](https://www.elastic.co/guide/en/logstash/current/config-setting-files.html)
  * [Configuring Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html)
  * [Configure Kibana](https://www.elastic.co/guide/en/kibana/curren