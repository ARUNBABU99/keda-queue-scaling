**Event-Driven Autoscaling in Kubernetes using KEDA**

This repo shows how to scale Kubernetes pods based on workload events instead of CPU or memory.

We simulate a common production pattern:

Queue → Worker Pods
         ↑
        KEDA

When jobs enter the queue → scale up
When no jobs → scale to zero

**Why KEDA?**

Traditional HPA reacts to resource usage.

But many services are idle most of the time:

order processors

payment workers

email senders

log consumers

background jobs

CPU remains low while work is actually pending in a queue.

👉 KEDA scales based on where the work originates (events), not CPU.

Official docs: https://keda.sh

**What this demo uses**

Redis as job queue

Python worker consuming jobs

KEDA Redis scaler

**Files in this repo**
| File              | Description               |
| ----------------- | ------------------------- |
| redis-master.yaml | Redis queue               |
| consumer.yaml     | Worker consuming messages |
| scaledobject.yaml | KEDA scaling config       |


Run locally (Minikube)
Start cluster
minikube start
minikube addons enable metrics-server  # Not needed in managed clusters like GKE/AKS

**Install KEDA**
helm repo add kedacore https://kedacore.github.io/charts
helm repo update
helm install keda kedacore/keda --namespace keda --create-namespace

**Deploy demo**
kubectl create ns demo
kubectl apply -f redis-master.yaml
kubectl apply -f consumer.yaml
kubectl apply -f scaledobject.yaml

**Trigger scaling**

Create temporary producer:

kubectl run redis -it --rm --image redis -- bash


**Inside the pod:**


redis-cli -h redis.demo.svc.cluster.local
LPUSH orders job1 job2 job3 job4 job5 job6 job7 job8 job9 job10

Watch scaling
kubectl get pods -n demo -w


Pods scale:

0 → N → 0

How it works (simplified)
Queue length
     ↓
KEDA Scaler
     ↓
External Metrics API
     ↓
HPA
     ↓
Deployment


KEDA doesn’t replace HPA
It feeds external metrics into HPA

Supported integrations

**KEDA supports many event sources:**

Kafka

RabbitMQ

Redis

Prometheus

AWS SQS

Azure Service Bus

Google Pub/Sub

Cron

HTTP

and more

**Full list:**
https://keda.sh/docs/latest/scalers/
