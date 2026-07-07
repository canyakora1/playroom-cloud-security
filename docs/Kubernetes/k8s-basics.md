---
title: Kubernetes Overview
---

# Kubernetes Overview

![k8s](../../.gitbook/assets/k8s.png)

Kubernetes (often shortened to **K8s**) is an open-source platform for running and managing **containerized applications** at scale.

It automates key operational tasks like **deploying** apps, **scaling** them up or down, **load balancing** traffic, and **recovering** from failures.

At a high level, you describe the desired state of your application (for example, how many replicas should run, which container image to use, and what resources it needs), and Kubernetes continuously works to match that state across a cluster of machines. Core concepts include **clusters** (the overall system), **nodes** (machines that run workloads), **pods** (the smallest deployable unit, typically one or more containers), and **services** (stable networking endpoints for reaching pods).

Kubernetes is widely used to build reliable microservices and cloud-native systems because it provides consistent orchestration across environments—on a laptop, in a data center, or in the cloud.

### Sample Kubernetes Deployment file

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

```yaml
# Create a deployment
kubectl apply -f deployment.yml
deployment.apps/demo-deployment created

# View deployments
kubectl get deployments
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
demo-deployment   3/3     3            3           29s

# To view all our nginx pods
kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
demo-deployment-77bc6bd484-7bxgs   1/1     Running   0          49s
demo-deployment-77bc6bd484-jzcbh   1/1     Running   0          49s
demo-deployment-77bc6bd484-zvfl4   1/1     Running   0          49s
```
