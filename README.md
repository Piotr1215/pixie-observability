# Kubernetes Observability with Pixie

Observability of decentralized systems has always been challenging. Dealing with latency, distributed transactions, failures etc became increasingly complex.
The more abstraction a decentralized system has, the more difficult it is to reason about it, debug and troubleshoot.

[Pixie Docs](https://docs.px.dev/installing-pixie/)

[Pixie Repo](https://github.com/pixie-io/pixie)

![pixie-donated-cncf](_media/pixie-donated-cncf.png)

## Challenges with debugging on K8s

The main reason that makes Kubernetes observability so difficult is the volitile and dynamic nature of workloads and resources. Instead of dealing wtih one server, we now deal with unknown number of servers (due to autoscalling). Rather than having one monolithic application, we now have multiple distributed services. Same with databases, which often reside outside of the cluster. 

Let's imagine making an HTTP(s) call to an API running on a Kubernetes cluster hosted on a cloud provider.

Simplified: Client -> Cloud Load Balancer -> Ingress -> Service -> Pod -> ContainerA -> ServiceB -> PodB -> ContianerB -> Database URL -> ContainerB -> PodB -> Service B -> ServiceA -> PodA -> ContainerA -> Egress -> Client

At any point of this communication chain things can go wrong, performance can degrade, security issues might occur, etc etc. Knowledge of what is happening on the cluster and detailed insights into each and every step of the communication chain is essencial for operational performance. 

### Where to observe

Now we know what to observe, but the question is how and where do we put our observability points, gateways of insight.

There are a couple of options:

- built the observability into service and code. This brings a high degree of control, but is cumbersome to maintain and not scalable.
- use sidecar pattern to inject observability logic into every pod. Better, but this can cause performance issues and is difficult to develop, as some different workloads need different metrics which can change over time
- use low level system calls to monitor usage of common protocols, stdout and stderr. This means installing something on the cluster itself, which is better, but often means giving this something extended priviledges and ingerenting in Linux Kernel
- use low level system calls with eBPF probes. This brings high extensibility and low overhead.

Combine this with export more important metrics into Prometheus and you are good to go.

## Installing Pixie

Pixie is an open source observability tool for Kubernetes applications. Pixie uses eBPF to automatically capture telemetry data without the need for manual instrumentation.

Choose docker option for Pixie CLI

```bash
alias px="docker run -i --rm -v ${HOME}/.pixie:/root/.pixie pixielabs/px"
```

## Installing Minikube

> Pixie currently only supports minikube, following install instructions are for Debian Linux
> Other installation instructions available at [Minikube Page](https://minikube.sigs.k8s.io/docs/start/#installation)

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube_latest_amd64.deb
sudo dpkg -i minikube_latest_amd64.deb
```

Start minikube

minikube start --driver=kvm2 --cni=flannel --cpus=4 --memory=8000 -p=pixie-cluster

px auth login and create account

deployment key

export PIXIE_DEPLOY_KEY=

px deploy-key create

helm install pixie pixie-operator/pixie-operator-chart --set deployKey=$PIXIE_DEPLOY_KEY --set clusterName=pixie-cluster --namespace pl --create-namespace

Probably need to install like that: https://docs.px.dev/installing-pixie/install-guides/community-cloud-for-pixie

## What is eBPF

![pixie-ebpf](_media/pixie-ebpf.png)

## Use Cases

### Monitor Network Traffic

### HTTP(s) Request Tracing

## Data Query

PiXL Language

## Tidbids

- data stays on cluster
- cilium is a similar project

## Standards

### Open Telemetry

https://opentelemetry.io/
