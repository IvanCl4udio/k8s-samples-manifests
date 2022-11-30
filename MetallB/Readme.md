# MetalLB #

MetalLB is a load-balancer implementation for bare metal Kubernetes clusters, using standard routing protocols.

For more information about it access: https://metallb.org/

---
## Problem: How configure the range IP to be used for MetalLB ##

After you had installed MetalLB and create new Services **you need first** configure which it will be the range of IPs will be available to be used by Metallb.

Here you will find a simple configuration that will set the IP range between: **10.0.0.200 and 10.0.0.250**.

> Important: You must have a DHCP server to provide these IPs to MetallB use it.

## Instructions ##

1. After you had install MetalLB on your cluster wait until all pods are in running state on namespace metallb-system.

2. Apply manifest file with the following command :

```bash
kubectl apply -f configmap-range-ip-metallb.yaml
```

For new versions from Metallb use configl2.yaml instead.
