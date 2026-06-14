# MetalLB Test Commands

## Check K3s Node IP

```bash
hostname -I
kubectl get nodes -o wide
```

## Install MetalLB

```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml
kubectl get pods -n metallb-system
```

## Apply MetalLB Configuration

```bash
kubectl apply -f ip-address-pool.yaml
kubectl apply -f l2-advertisement.yaml
```

## Deploy Nginx LoadBalancer Test

```bash
kubectl apply -f nginx-deployment.yaml
kubectl apply -f nginx-loadbalancer-service.yaml
```

## Verify

```bash
kubectl get svc
kubectl get pods -o wide
```

## Access

```bash
curl http://<EXTERNAL-IP>
```

or open in browser:

```text
http://<EXTERNAL-IP>
```

## Cleanup

```bash
kubectl delete -f nginx-loadbalancer-service.yaml
kubectl delete -f nginx-deployment.yaml
kubectl delete -f l2-advertisement.yaml
kubectl delete -f ip-address-pool.yaml
```
