# NodePort Test Commands

Apply:

```bash
kubectl apply -f 02-nodeport/deployment.yaml
kubectl apply -f 02-nodeport/service.yaml
```

Check:

```bash
kubectl get svc frontend-service -n networking-lab
kubectl get pods -n networking-lab -o wide
```

Get node IP:

```bash
hostname -I
```

Access from browser:

```bash
http://<NODE-IP>:30080
```

Example:

```bash
http://172.16.129.234:30080
```

Expected result:

```bash
Nginx welcome page
```
