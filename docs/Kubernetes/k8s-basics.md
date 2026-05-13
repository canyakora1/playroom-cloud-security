---
title: Kubernetes Overview
---

![k8s](../assets/images/k8s.png)

Kubernetes (often shortened to **K8s**) is an open-source platform for running and managing **containerized applications** at scale. 

It automates key operational tasks like **deploying** apps, **scaling** them up or down, **load balancing** traffic, and **recovering** from failures.

At a high level, you describe the desired state of your application (for example, how many replicas should run, which container image to use, and what resources it needs), and Kubernetes continuously works to match that state across a cluster of machines. Core concepts include **clusters** (the overall system), **nodes** (machines that run workloads), **pods** (the smallest deployable unit, typically one or more containers), and **services** (stable networking endpoints for reaching pods).

Kubernetes is widely used to build reliable microservices and cloud-native systems because it provides consistent orchestration across environments—on a laptop, in a data center, or in the cloud.

## Sample Kubernetes Deployment file

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
  labels:
    app: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
        - name: webapp
          image: nginx:1.27
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: "100m"
              memory: "128Mi"
            limits:
              cpu: "500m"
              memory: "256Mi"
          readinessProbe:
            httpGet:
              path: /
              port: 80
            initialDelaySeconds: 5
            periodSeconds: 10
          livenessProbe:
            httpGet:
              path: /
              port: 80
            initialDelaySeconds: 15
            periodSeconds: 20

```
