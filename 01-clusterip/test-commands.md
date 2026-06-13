# ClusterIP Test Commands

Apply:

```bash
kubectl apply -f 01-clusterip/deployment.yaml
kubectl apply -f 01-clusterip/service.yaml
```

Check:

```bash
kubectl get pods -n networking-lab
kubectl get svc -n networking-lab
```

Test from inside the cluster:

```bash
kubectl run testpod -n networking-lab --image=busybox -it --rm -- sh
```

Inside the pod:

```bash
wget -qO- http://employee-service
```

Expected result:

```bash
Welcome to nginx
```
