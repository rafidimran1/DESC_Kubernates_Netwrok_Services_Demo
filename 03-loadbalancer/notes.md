# LoadBalancer Notes for K3s

Apply:

```bash
kubectl apply -f 03-loadbalancer/deployment.yaml
kubectl apply -f 03-loadbalancer/service.yaml
```

Check:

```bash
kubectl get svc company-lb -n networking-lab
```

In cloud Kubernetes, LoadBalancer usually gets a public external IP.

In local K3s, this depends on ServiceLB. K3s usually includes ServiceLB by default.

If EXTERNAL-IP is pending, options are:

1. Use NodePort instead.
2. Install MetalLB.
3. Confirm ServiceLB is enabled.

Check K3s system pods:

```bash
kubectl get pods -n kube-system
```
