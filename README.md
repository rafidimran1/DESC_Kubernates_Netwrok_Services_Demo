# DESC_Kubernates_Netwrok_Services_Demo

## K3s Networking Lab: Services and Ingress

## Project Scenario

You are a junior DevOps engineer working for a company that is migrating its applications to Kubernetes using **K3s**.

Your task is to deploy and test different Kubernetes networking methods:

- **ClusterIP** for internal-only services
- **NodePort** for direct access using node IP and port
- **LoadBalancer** for cloud-style exposure
- **Ingress** for hostname-based routing

This lab is designed for students who already know basic Kubernetes commands such as:

```bash
kubectl get pods
kubectl get svc
kubectl apply -f file.yaml
kubectl delete -f file.yaml
```

---

## Learning Objectives

By completing this repository lab, students will understand:

1. How Kubernetes Services expose applications.
2. Difference between ClusterIP, NodePort, and LoadBalancer.
3. How Ingress routes HTTP traffic using domain names.
4. How labels and selectors connect Services to Pods.
5. How to test internal and external service connectivity in K3s.

---

## Prerequisites

You need:

- Ubuntu VM or Linux machine
- K3s installed and running
- kubectl configured
- Internet access for pulling container images

Verify K3s:

```bash
kubectl get nodes
```

Expected example:

```bash
NAME    STATUS   ROLES           VERSION
desc1   Ready    control-plane   v1.35.5+k3s1
```

Create the namespace:

```bash
kubectl apply -f namespace.yaml
```

---

# Repository Structure

```bash
k3s-networking-services-ingress-lab/
│
├── README.md
├── namespace.yaml
├── cleanup.md
│
├── 01-clusterip/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── test-commands.md
│
├── 02-nodeport/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── test-commands.md
│
├── 03-loadbalancer/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── notes.md
│
├── 04-ingress/
│   ├── hr-deployment.yaml
│   ├── finance-deployment.yaml
│   ├── hr-service.yaml
│   ├── finance-service.yaml
│   ├── ingress.yaml
│   └── test-commands.md
│
└── docs/
    ├── concepts.md
    └── expected-output.md
```

---

# Task 1: ClusterIP Service

## Scenario

The company has an internal employee portal. This portal should only be accessible by applications inside the Kubernetes cluster.

## Goal

Deploy an internal Nginx application and expose it using a **ClusterIP** service.

## Deploy

```bash
kubectl apply -f 01-clusterip/deployment.yaml
kubectl apply -f 01-clusterip/service.yaml
```

## Verify

```bash
kubectl get pods -n networking-lab
kubectl get svc -n networking-lab
```

## Test Internal Access

Create a temporary test pod:

```bash
kubectl run testpod -n networking-lab --image=busybox -it --rm -- sh
```

Inside the test pod:

```bash
wget -qO- http://employee-service
```

## Expected Result

You should see Nginx HTML output.

## Key Concept

ClusterIP gives a service an internal IP address. It is not directly accessible from outside the cluster.

---

# Task 2: NodePort Service

## Scenario

The development team wants to access a test frontend website using the Kubernetes node IP and port.

## Goal

Deploy Nginx and expose it using a **NodePort** service.

## Deploy

```bash
kubectl apply -f 02-nodeport/deployment.yaml
kubectl apply -f 02-nodeport/service.yaml
```

## Check Service

```bash
kubectl get svc frontend-service -n networking-lab
```

Expected example:

```bash
NAME               TYPE       CLUSTER-IP     PORT(S)
frontend-service   NodePort   10.43.x.x      80:30080/TCP
```

## Get Node IP

```bash
hostname -I
```

or:

```bash
kubectl get nodes -o wide
```

## Access in Browser

```bash
http://<NODE-IP>:30080
```

Example:

```bash
http://172.16.129.234:30080
```

## Key Concept

NodePort exposes the application on every Kubernetes node using a port between 30000 and 32767.

---

# Task 3: LoadBalancer Service

## Scenario

The company wants cloud-style external access to a website.

## Goal

Expose an application using a **LoadBalancer** service.

## Deploy

```bash
kubectl apply -f 03-loadbalancer/deployment.yaml
kubectl apply -f 03-loadbalancer/service.yaml
```

## Check

```bash
kubectl get svc company-lb -n networking-lab
```

## Important K3s Note

K3s includes a lightweight service load balancer called **ServiceLB**. On many local K3s setups, a LoadBalancer service may get an external IP from the node.

If `EXTERNAL-IP` stays pending, use NodePort or install MetalLB.

## Key Concept

LoadBalancer is normally used in cloud Kubernetes platforms where the cloud provider gives a public IP.

---

# Task 4: Ingress

## Scenario

The company wants to access multiple applications using domain names instead of NodePort numbers.

Applications:

| Application | URL |
|---|---|
| HR Portal | `hr.company.local` |
| Finance Portal | `finance.company.local` |

## Goal

Use **Ingress** to route traffic based on hostname.

## Deploy

```bash
kubectl apply -f 04-ingress/hr-deployment.yaml
kubectl apply -f 04-ingress/finance-deployment.yaml
kubectl apply -f 04-ingress/hr-service.yaml
kubectl apply -f 04-ingress/finance-service.yaml
kubectl apply -f 04-ingress/ingress.yaml
```

## Check

```bash
kubectl get ingress -n networking-lab
```

## Add Local DNS Entries

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

Example:

```bash
172.16.129.234 hr.company.local
172.16.129.234 finance.company.local
```

## Test

```bash
curl http://hr.company.local
curl http://finance.company.local
```

## Key Concept

Ingress is used for HTTP/HTTPS routing. It is better than exposing every application using separate NodePorts.

---

# Student Deliverables

Students should submit screenshots of:

1. `kubectl get pods -n networking-lab`
2. `kubectl get svc -n networking-lab`
3. ClusterIP internal test
4. NodePort browser access
5. LoadBalancer service output
6. Ingress output
7. `curl http://hr.company.local`
8. `curl http://finance.company.local`

Students should also answer:

1. What is the difference between ClusterIP and NodePort?
2. Why is LoadBalancer usually used in cloud environments?
3. Why is Ingress better than exposing many NodePort services?
4. How does a Service know which Pods to send traffic to?
5. What is the role of labels and selectors?

---

# Cleanup

To remove everything:

```bash
kubectl delete namespace networking-lab
```
