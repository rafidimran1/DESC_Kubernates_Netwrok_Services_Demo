# Networking Concepts Explained

## ClusterIP

ClusterIP is the default Kubernetes Service type.

It gives the service an internal cluster IP. Only Pods inside the cluster can access it.

Use case:

- Backend APIs
- Databases
- Internal microservices

Example:

```bash
frontend pod ---> backend ClusterIP service ---> backend pods
```

---

## NodePort

NodePort exposes a service on a port of every Kubernetes node.

Port range is usually:

```bash
30000-32767
```

Use case:

- Testing
- Small labs
- Quick external access

Example:

```bash
browser ---> node-ip:30080 ---> service ---> pod
```

---

## LoadBalancer

LoadBalancer asks the infrastructure provider to create an external load balancer.

In cloud environments, this usually gives a public IP.

In K3s, ServiceLB may provide this behavior locally.

Use case:

- Public application exposure
- Cloud Kubernetes platforms

---

## Ingress

Ingress routes HTTP and HTTPS traffic to services.

It allows multiple apps to share the same external entry point.

Example:

```bash
hr.company.local      ---> hr-service
finance.company.local ---> finance-service
```

Ingress needs an Ingress Controller.

In K3s, Traefik is usually installed by default.

---

## Labels and Selectors

A Service does not directly point to Pod names.

Instead, it uses selectors.

Example:

```yaml
selector:
  app: frontend
```

This means the Service sends traffic to Pods with:

```yaml
labels:
  app: frontend
```
