# Honeypot setup

## Setup (Kind + Cilium)

### Create kind-config.yaml

```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
- role: worker
networking:
  disableDefaultCNI: true
  podSubnet: "10.10.0.0/16"
  serviceSubnet: "10.11.0.0/16"
```

kind create cluster --name c1 --config=kind-config.yaml

### Install the Cilium CLI

https://docs.cilium.io/en/v1.12/gettingstarted/k8s-install-default/#install-the-cilium-cli

### Connect to Cluster

> kubectl cluster-info --context kind-c1

### Install Cilium

```
helm repo add cilium https://helm.cilium.io/
docker pull quay.io/cilium/cilium:v1.12.3
kind load docker-image quay.io/cilium/cilium:v1.12.3 --name c1
```

```
helm install cilium cilium/cilium --version 1.12.3 \
   --namespace kube-system \
   --set image.pullPolicy=IfNotPresent \
   --set ipam.mode=kubernetes
```

### Check status

```
cilium status --wait
cilium connectivity test
```
```
helm upgrade cilium cilium/cilium --version 1.12.3 \
   --namespace kube-system \
   --reuse-values \
   --set hubble.relay.enabled=true \
   --set hubble.ui.enabled=true
```

## Hubble Setup 

https://docs.cilium.io/en/v1.12/gettingstarted/hubble_setup/#install-the-hubble-client

```
cilium hubble port-forward&
hubble status
hubble observe
```
