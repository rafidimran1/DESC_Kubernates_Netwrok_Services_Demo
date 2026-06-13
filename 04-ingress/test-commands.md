# Ingress Test Commands

Apply:

```bash
kubectl apply -f 04-ingress/hr-deployment.yaml
kubectl apply -f 04-ingress/finance-deployment.yaml
kubectl apply -f 04-ingress/hr-service.yaml
kubectl apply -f 04-ingress/finance-service.yaml
kubectl apply -f 04-ingress/ingress.yaml
```

Check Traefik in K3s:

```bash
kubectl get pods -n kube-system | grep traefik
```

Check ingress:

```bash
kubectl get ingress -n networking-lab
```

Find node IP:

```bash
hostname -I
```

Edit hosts file:

```bash
sudo nano /etc/hosts
```

Add:

```bash
<NODE-IP> hr.company.local
<NODE-IP> finance.company.local
```

Test:

```bash
curl http://hr.company.local
curl http://finance.company.local
```

Expected result:

```bash
Nginx welcome page
```
