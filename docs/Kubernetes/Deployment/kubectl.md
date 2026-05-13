---
title: Kubernetes Dev Cluster — Vagrant
---

A local three-node Kubernetes cluster running on Ubuntu 22.04 (Jammy) VMs, provisioned with Vagrant and VirtualBox. Version 2 adds a full optional addon layer: DNS caching, a Web UI, resource monitoring, log aggregation, ingress, TLS, and a private container registry.

```
┌──────────────────────────────────────────────────────────────┐
│                        Host Machine                          │
│                                                              │
│  ┌───────────────┐   ┌──────────────┐   ┌──────────────┐    │
│  │  k8s-control  │   │ k8s-worker-1 │   │ k8s-worker-2 │    │
│  │ 192.168.56.10 │   │192.168.56.11 │   │192.168.56.12 │    │
│  │  2 CPU / 2 GB │   │ 2 CPU / 2 GB │   │ 2 CPU / 2 GB │    │
│  └──────┬────────┘   └──────┬───────┘   └──────┬───────┘    │
│         └──────────────────┴──────────────────┘             │
│                      Private Network                         │
└──────────────────────────────────────────────────────────────┘
```

For scripts used in this demo, go to the `Playroom Security GitHub` repositoty here:
<div class="iframely-embed"><div class="iframely-responsive" style="height: 140px; padding-bottom: 0;"><a href="https://github.com/playroom-security/Kubernetes-Dev-Cluster-Vagrant" data-iframely-url="https://iframely.net/DagsmOM5?card=small&theme=dark"></a></div></div><script async src="https://iframely.net/embed.js"></script>

---

## What's New in v2

| Category | Addon | Default |
|---|---|---|
| DNS | NodeLocal DNSCache | ✅ On |
| Web UI | Kubernetes Dashboard | ✅ On |
| Resource monitoring | Metrics Server | ✅ On |
| Resource monitoring | Prometheus + Grafana | ⬜ Off |
| Cluster logging | Loki + Promtail | ⬜ Off |
| Ingress | ingress-nginx | ✅ On |
| TLS | cert-manager + self-signed issuer | ⬜ Off |
| Registry | In-cluster Docker Registry | ⬜ Off |
| Network | Cilium support added | — |

All addons are toggled in `settings.yaml` — no script editing required.

---

## Prerequisites

| Tool | Version | Notes |
|---|---|---|
| [Vagrant](https://developer.hashicorp.com/vagrant/downloads) | ≥ 2.3 | |
| [VirtualBox](https://www.virtualbox.org/wiki/Downloads) | ≥ 7.0 | |
| vagrant-reload plugin | any | `vagrant plugin install vagrant-reload` |
| Free RAM (default addons) | ≥ 8 GB | 3 × 2 GB + host overhead |
| Free RAM (with Prometheus) | ≥ 12 GB | increase worker memory to 3072 MB |
| Free Disk | ≥ 30 GB | ~10 GB per VM |

---

## Project Structure

```
k8s-dev/
├── settings.yaml        # All configuration and addon toggles
├── Vagrantfile          # VM definitions, passes addon env vars to install.sh
├── install.sh           # Node bootstrap + addon installer
└── README.md

# Generated at runtime (do not commit):
├── join.sh              # kubeadm join token (written by control-plane)
├── admin.conf           # kubeconfig for local kubectl access
└── dashboard-token.txt  # Dashboard bearer token (if addon enabled)
```

---

## Configuration — `settings.yaml`

### Kubernetes & nodes

```yaml
kubernetes:
  version: "1.29"
  pod_cidr: "10.244.0.0/16"
  cni: "flannel"           # flannel | calico | cilium

nodes:
  control_plane:
    ip: "192.168.56.10"
    cpu: 2
    memory: 2048
  workers:
    - name: "k8s-worker-1"
      ip: "192.168.56.11"
      cpu: 2
      memory: 2048
```

### CNI options

| CNI | `cni` value | `pod_cidr` | Notes |
|---|---|---|---|
| Flannel | `flannel` | `10.244.0.0/16` | Simple, default |
| Calico | `calico` | `192.168.0.0/16` | NetworkPolicy support |
| Cilium | `cilium` | `10.244.0.0/16` | eBPF-based, feature-rich |

### Addons

Every addon has an `enabled` flag. Flip it to `true`/`false` and rebuild.

```yaml
addons:
  nodelocal_dns:        { enabled: true }
  metrics_server:       { enabled: true,  version: "v0.7.1" }
  dashboard:            { enabled: true,  version: "v2.7.0" }
  ingress_nginx:        { enabled: true,  version: "v1.10.1",
                          http_nodeport: 30080, https_nodeport: 30443 }
  prometheus_grafana:   { enabled: false, grafana_password: "admin" }
  loki:                 { enabled: false }   # requires prometheus_grafana
  cert_manager:         { enabled: false, version: "v1.14.5" }
  registry:             { enabled: false, nodeport: 32000 }
```

> **Loki** requires `prometheus_grafana.enabled: true` — it adds a datasource
> to the Grafana instance deployed by that addon.

---

## Quick Start

### 1. Install the Vagrant plugin

```bash
vagrant plugin install vagrant-reload
```

### 2. Review `settings.yaml`

Enable or disable addons to match your available RAM. The defaults (DNS cache,
Metrics Server, Dashboard, ingress-nginx) work comfortably within 8 GB.

### 3. Start the cluster

```bash
# Recommended: control-plane first so join.sh exists before workers start
vagrant up k8s-control
vagrant up k8s-worker-1 k8s-worker-2

# Or all at once (Vagrant will handle ordering)
vagrant up
```

> First run takes **15–25 minutes** depending on internet speed and enabled addons.

### 4. Verify

```bash
export KUBECONFIG=$(pwd)/admin.conf

kubectl get nodes
kubectl get pods -A
```

Expected nodes output (allow ~90 s for `Ready`):

```
NAME           STATUS   ROLES           AGE   VERSION
k8s-control    Ready    control-plane   6m    v1.29.x
k8s-worker-1   Ready    <none>          4m    v1.29.x
k8s-worker-2   Ready    <none>          4m    v1.29.x
```

---

## Addon Reference

### NodeLocal DNSCache

Runs a DNS caching agent on every node at `169.254.20.10`, reducing latency and CoreDNS load.

```bash
# Verify DaemonSet is running
kubectl get ds node-local-dns -n kube-system
```

---

### Metrics Server

Enables `kubectl top` and the HorizontalPodAutoscaler.

```bash
kubectl top nodes
kubectl top pods -A
```

---

### Kubernetes Dashboard

Browser-based cluster management UI. The bearer token is written to `dashboard-token.txt` in your project directory.

```bash
# Get the NodePort
kubectl get svc kubernetes-dashboard -n kubernetes-dashboard

# Get the token (also saved to ./dashboard-token.txt)
cat dashboard-token.txt
```

Open `https://192.168.56.10:<nodeport>` in your browser, accept the self-signed certificate warning, and paste the token.

### Dashboard Token Renewal
To renew the `Dashboard Token` if it ever expires:

```
kubectl -n kubernetes-dashboard create token admin-user --duration=720h
```
**__Maximum duration__** = 720h

---

### ingress-nginx

A single entry point for HTTP/HTTPS traffic into the cluster.

```bash
# Verify controller is running
kubectl get pods -n ingress-nginx

# Example Ingress resource
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-app
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: my-app.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app-svc
            port:
              number: 80
EOF
```

Add `192.168.56.10  my-app.local` to your host `/etc/hosts`, then access via `http://my-app.local:30080`.

---

### cert-manager

Automates TLS certificate issuance. A `selfsigned-issuer` ClusterIssuer is created automatically.

```bash
# Request a certificate
cat <<EOF | kubectl apply -f -
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: my-cert
  namespace: default
spec:
  secretName: my-cert-tls
  issuerRef:
    name: selfsigned-issuer
    kind: ClusterIssuer
  dnsNames:
  - my-app.local
EOF

kubectl get certificate my-cert
```

---

### Prometheus + Grafana

Full metrics stack deployed via `kube-prometheus-stack`. Pre-built dashboards for nodes, pods, and workloads are included out of the box.

| Service | URL | Credentials |
|---|---|---|
| Grafana | `http://192.168.56.10:30300` | admin / `<grafana_password>` |
| Prometheus | `http://192.168.56.10:30090` | — |
| Alertmanager | `http://192.168.56.10:30093` | — |

> Requires workers to have at least **3072 MB** RAM. Update `settings.yaml` before enabling.

```bash
# Check all monitoring pods are running
kubectl get pods -n monitoring
```

---

### Loki + Promtail

Lightweight log aggregation. Promtail ships logs from all pods to Loki; query them in Grafana under **Explore → Loki**.

```bash
# Verify Promtail DaemonSet
kubectl get ds promtail -n monitoring

# Quick log query via Grafana Explore:
# {namespace="default"} |= "error"
```

> Requires `prometheus_grafana.enabled: true`.

---

### Container Registry

An in-cluster Docker Registry exposed as a NodePort. Useful for pushing locally built images without Docker Hub.

```bash
# Push an image (from your host)
docker tag myapp 192.168.56.10:32000/myapp:latest
docker push 192.168.56.10:32000/myapp:latest

# Pull from inside the cluster
# image: 192.168.56.10:32000/myapp:latest
```

> containerd on every node is pre-configured to allow insecure access to this registry.

---

## Daily Usage

### SSH into a node

```bash
vagrant ssh k8s-control
vagrant ssh k8s-worker-1
vagrant ssh k8s-worker-2
```

### kubectl from your host

```bash
export KUBECONFIG=$(pwd)/admin.conf
# Or add to ~/.bashrc / ~/.zshrc to make it permanent
```

### Stop and restart (preserves state)

```bash
vagrant halt
vagrant up
```

### Reprovision a single node

```bash
vagrant provision k8s-control
```

### Destroy and rebuild from scratch

```bash
vagrant destroy -f
vagrant up
```

---

## Resource Guide

Use this table to plan your `settings.yaml` memory values.

| Addons enabled | Recommended worker RAM |
|---|---|
| Defaults only (DNS, Metrics, Dashboard, ingress-nginx) | 2048 MB |
| + cert-manager or Registry | 2048 MB |
| + Prometheus + Grafana | 3072 MB |
| + Prometheus + Grafana + Loki | 4096 MB |

---

## Troubleshooting

### Nodes stuck in `NotReady`

CNI or CoreDNS may still be initialising. Wait 90 seconds and recheck:

```bash
kubectl get nodes
kubectl get pods -n kube-system
```

### Addon pod in `CrashLoopBackOff`

```bash
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace> --previous
```

### `join.sh` not found on worker

Provision the control-plane first:

```bash
vagrant provision k8s-control
vagrant provision k8s-worker-1 k8s-worker-2
```

### Dashboard shows no metrics

Ensure Metrics Server is enabled and running:

```bash
kubectl get pods -n kube-system | grep metrics-server
kubectl top nodes
```

### VirtualBox host-only network conflict

Change node IPs in `settings.yaml` to an unused subnet (e.g. `192.168.100.x`) and rebuild:

```bash
vagrant destroy -f && vagrant up
```

---

## Extending the Cluster

### Add a worker node

Add an entry to `nodes.workers` in `settings.yaml`:

```yaml
- name: "k8s-worker-3"
  ip: "192.168.56.13"
  cpu: 2
  memory: 2048
```

Then:

```bash
vagrant up k8s-worker-3
```

### Upgrade Kubernetes

Update `kubernetes.version` in `settings.yaml`, destroy, and rebuild:

```bash
vagrant destroy -f
vagrant up
```

---

