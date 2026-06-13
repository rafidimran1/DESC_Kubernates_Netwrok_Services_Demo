# Expected Outputs

## Nodes

```bash
kubectl get nodes
```

Expected:

```bash
NAME    STATUS   ROLES           VERSION
desc1   Ready    control-plane   v1.35.5+k3s1
```

## Services

```bash
kubectl get svc -n networking-lab
```

Expected services:

```bash
employee-service    ClusterIP
frontend-service    NodePort
company-lb          LoadBalancer
hr-service          ClusterIP
finance-service     ClusterIP
```

## Ingress

```bash
kubectl get ingress -n networking-lab
```

Expected:

```bash
company-ingress   hr.company.local,finance.company.local
```

## Pods

```bash
kubectl get pods -n networking-lab
```

Expected:

```bash
employee-portal   Running
frontend          Running
company-website   Running
hr-portal         Running
finance-portal    Running
```
