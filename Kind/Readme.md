# KIND #


## Simple Kind Cluster with Ingress and Load Balancer ##

### First Create a cluster ###

Create a yaml file called cluster-ingress-loadbalancer.yaml with the content below.

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
- role: worker
  extraMounts:
  - hostPath: /var/shared-storage/www
    containerPath: /www
```

Execute the command to create a new cluster with kind:

```bash
kind create cluster --name cluster-001 --config cluster-ingress-loadbalancer.yaml
```

### Deploy NGInx Ingress ###

Apply the official manifest for kind using the following command:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```
### Deploy Metallb Load Balancer ###

Create namespace for Metallb

```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/master/manifests/namespace.yaml
```

Create a secret

```bash
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

Apply the official manifest of Metallb

```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/master/manifests/metallb.yaml
```

If you are using Docker you should get network bridge to create a configmap to load balancer.

```bash
docker network inspect -f '{{.IPAM.Config}}' kind
```

Use the information you got on last step and create a configmap file:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 172.18.0.100-172.18.0.250
```

Last step apply the configmap that you created.

```bash
kubectl apply -f configmap.yaml
```

Enjoy.
