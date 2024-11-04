# CFK for a non K8s based CP cluster

This is a fork of the project https://github.com/tomasalmeida/cfk-control-plane-cp 

- [CFK for a non K8s based CP cluster](#cfk-for-a-non-k8s-based-cp-cluster)
  - [Disclaimer](#disclaimer)
  - [Setup](#setup)
  - [Test CFK applied to non K8s based cluster](#test-cfk-applied-to-non-k8s-based-cluster)
  - [Cleanup](#cleanup)


## Disclaimer

The code and/or instructions here available are **NOT** intended for production usage. 
It's only meant to serve as an example or reference and does not replace the need to follow actual and official documentation of referenced products.

## Setup

Start our non kubernetes based CP cluster (in this case based on docker compose):

```shell
cd cp
docker-compose up -d
cd ..
```

Let's deploy now a local K8s cluster leveraging `kind` and within the CFK operator:

```shell
cd cfk
kind create cluster --config kind-basic-cluster.yaml
kubectl cluster-info --context kind-kind
kubectl create namespace confluent
kubectl config set-context --current --namespace confluent
helm repo add confluentinc https://packages.confluent.io/helm --insecure-skip-tls-verify
helm repo update
helm upgrade --install confluent-operator confluentinc/confluent-for-kubernetes
kubectl get pods -A -o wide
cd ..
```

## Test CFK applied to non K8s based cluster

We can test the CFK operator against our non K8s based cluster with:

```shell
kubectl apply -f data/topics.yml
```

We can confirm topic `demo-topic-1` was created by executing:

```shell
kafka-topics --bootstrap-server localhost:29092 --list
```

## Cleanup

```shell
cd cfk
kind delete cluster 
cd ../cp
docker compose down -v
cd ..
```