# tarantool-load-testing

Table of contents:
1. [Introduction](#introduction)
2. [Environment](#environment)
3. [Tarantool Operator](#tarantol-operator)
4. [CRUD application](#crud-application)  
  4.1 [Topology](#topology)  
  4.2 [Deploy](#deploy)  
  4.3 [Scaling](#scaling)  
  4.4 [Clean up](#clean-up)  
  4.5 [Admin panel](#admin-panel)
5. [Deploy Prometheus and Grafana via Helm](#deploy-prometheus-and-grafana-via-helm)
6. [Load testing via Yandex.Tank](#load-testing-via-yandextank)  
  6.1 [Read test](#read-test)  
  6.2 [Write test](#write-test)  
  6.3 [Failover test](#failover-test)
7. WIP

## Introduction

This repository contains the final project of OTUS database management course.
For educational purposes only.

## Environment

- Kubernetes cluster v1.16.9
- 3 worker nodes (Ubuntu 18.04 LTS 64-bit/2 vCPU/8 GB/30 GB)
- Loader VM (Ubuntu 18.04 LTS 64-bit/8 vCPU/16 GB RAM/5 GB)  

Loader VM is used to run Yandex.Tank load testing from.

Kubernetes cluster and loader VM created in [Selectel Cloud Platform](https://selectel.ru/en/services/cloud/)

## Tarantol Operator

Tarantool operator provides automation that simplifies the administration of Tarantool 
Cartridge-based clusters on Kubernetes.

Apply RBAC manifests:
```bash
kubectl create -f https://raw.githubusercontent.com/tarantool/tarantool-operator/0.0.1/deploy/service_account.yaml
kubectl create -f https://raw.githubusercontent.com/tarantool/tarantool-operator/0.0.1/deploy/role.yaml
kubectl create -f https://raw.githubusercontent.com/tarantool/tarantool-operator/0.0.1/deploy/role_binding.yaml
```

Apply Tarantool CRD's:
```bash
kubectl create -f https://raw.githubusercontent.com/tarantool/tarantool-operator/0.0.1/deploy/crds/tarantool_v1alpha1_cluster_crd.yaml
kubectl create -f https://raw.githubusercontent.com/tarantool/tarantool-operator/0.0.1/deploy/crds/tarantool_v1alpha1_role_crd.yaml
kubectl create -f https://raw.githubusercontent.com/tarantool/tarantool-operator/0.0.1/deploy/crds/tarantool_v1alpha1_replicasettemplate_crd.yaml
``` 

Apply operator:
```bash
kubectl create -f https://raw.githubusercontent.com/tarantool/tarantool-operator/0.0.1/deploy/operator.yaml
```

## CRUD Application

Test app is just a Tarantool-based distributed key-value storage. 
Each instance is running along with storage on the same level.

Originally taken from tarantool-operator examples:
https://github.com/tarantool/tarantool-operator/tree/master/examples/kv

### Topology

WIP

### Deploy

```bash
kubectl create -f kv-app/deployment.yaml
```

### Scaling

Add more storage:
```bash
kubectl scale roles.tarantool.io storage --replicas=3
``` 

Add more instances per shard:
```bash
kubectl edit replicasettemplates.tarantool.io storage-template
```

Change ```.spec.replicas``` to desired number of instances per shard.

### Clean up

```bash
kubectl delete clusters.tarantool.io examples-kv-cluster
```

### Admin panel

WIP

## Deploy Prometheus and Grafana via Helm

Prometheus + Grafana are used to collect and represent a plenty of metrics in a human-friendly way. 
In our case we will check worker nodes resource utilization.

```bash
helm install prometheus stable/prometheus --set alertmanager.enabled=false \
                                          --set server.persistentVolume.enabled=false \
                                          --set pushgateway.enabled=false \
                                          --set configmapReload.prometheus.enabled=false
```

```bash
helm install grafana stable/grafana
```

## Load testing via Yandex.Tank

WIP

- Read test
- Write test
- Failover test (write but with killed masters)

## Results

WIP
