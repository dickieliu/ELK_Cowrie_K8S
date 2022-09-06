# ELK on K8S

 쿠버네티스 환경에 ELK stack 배포하기

<br>

## 오브젝트 생성
* namespace 생성
```
# !/bin/sh
kubectl create -f ./namespace.yaml
```

<br>

* 리소스 생성 및 업데이트
```
# !/bin/sh
kubectl apply -f ./logstash-configmap.yaml
kubectl apply -f ./kibana-configmap.yaml
kubectl apply -f ./elasticsearch.yaml
kubectl apply -f ./logstash.yaml
kubectl apply -f ./kibana.yaml
```

<br>

## 확인

* Elasticsearch 접속 확인
```
# !/bin/sh
curl -X GET "http://localhost:9200"
```
```
{
  "name" : "7709ec1b438b",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "iPGC1QefSwK00RCqpRnmfQ",
  "version" : {
    "number" : "7.15.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "93d5a7f6192e8a1a12e154a2b81bf6fa7309da0c",
    "build_date" : "2021-11-04T14:04:42.515624022Z",
    "build_snapshot" : false,
    "lucene_version" : "8.9.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

<br>

* Kibana 접속 확인: `http://localhost:5601` 접속

<br>

## ELK stack 참고

* image
  * Elasticsearch, Kibana: elastic에서 제공하는 기본 이미지 사용
  * Logstash: 추가 플러그인 설치로 인해 base image를 바탕으로 [새로운 이미지](https://hub.docker.com/repository/docker/sir0123/logstash) 생성

<br>

* configuration 관련 참고
  * [Install Elasticsearch on Docker](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)
  * [Install Kibana on Docker](https://www.elastic.co/guide/en/kibana/current/docker.html)
  * [Run Logstash on Docker](https://www.elastic.co/guide/en/logstash/current/docker.html)
  * [Logstash Configuration Files](https://www.elastic.co/guide/en/logstash/current/config-setting-files.html)
  * [Configuring Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html)
  * [Configure Kibana](https://www.elastic.co/guide/en/kibana/current/settings.html)
