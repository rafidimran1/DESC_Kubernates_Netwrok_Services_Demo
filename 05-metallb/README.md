# 05 - MetalLB Task for K3s

## Topic

MetalLB with K3s LoadBalancer Service

---

## Scenario

You are running a Kubernetes cluster using K3s in a local lab environment.

Your students already learned:

- ClusterIP
- NodePort
- LoadBalancer
- Ingress

Now they create a Service of type `LoadBalancer`, but the external IP may stay pending in local Kubernetes environments because there is no cloud provider like AWS, Azure, or Google Cloud to assign an external IP.

Your task is to install and configure MetalLB so LoadBalancer Services can receive usable external IP addresses in a local K3s lab.

---

## Learning Objectives

By completing this task, students will understand:

- Why LoadBalancer works automatically in cloud Kubernetes
- Why LoadBalancer may not work in local K3s
- What MetalLB does
- How MetalLB gives external IPs to LoadBalancer Services
- How MetalLB works with Ingress
- How to test a LoadBalancer service using Nginx

---

# Beginner Explanation

## The Problem

In cloud Kubernetes:

```text
AWS / Azure / GCP gives Kubernetes an external IP
```

So when you create:

```yaml
type: LoadBalancer
```

the cloud provider gives your service a public IP.

Example:

```text
nginx-service -> 54.21.10.5
```

But in local K3s:

```text
VMware / VirtualBox / Local Server / Home Lab
```

there is no cloud provider.

So the LoadBalancer Service may show:

```text
EXTERNAL-IP = <pending>
```

---

## What is MetalLB?

MetalLB is a software load balancer for bare-metal or local Kubernetes clusters.

It gives external IP addresses to Kubernetes LoadBalancer Services.

Think of it like this:

```text
Cloud Load Balancer = Receptionist provided by AWS/Azure/GCP
MetalLB = Receptionist you install yourself in your lab
```

---

## Real World Analogy

Imagine a company building.

LoadBalancer needs a receptionist.

In cloud:

```text
AWS gives receptionist
Azure gives receptionist
GCP gives receptionist
```

In your local K3s lab:

```text
No receptionist exists
```

MetalLB becomes that receptionist.

---

## Without MetalLB

```text
Browser
   |
   v
LoadBalancer Service
   |
   v
Pending External IP
```

---

## With MetalLB

```text
Browser
   |
   v
MetalLB IP
   |
   v
LoadBalancer Service
   |
   v
Pods
```

---

## Easy Memory Trick

```text
ClusterIP    = Private office room
NodePort     = Door in building wall
LoadBalancer = Receptionist
Ingress      = Security guard
MetalLB      = Hiring a receptionist for your local lab
```

---

# Important Before Starting

You must choose an IP range from your local network.

Example:

If your node IP is:

```text
172.16.129.234
```

then choose unused IPs from the same network, for example:

```text
172.16.129.240-172.16.129.250
```

Do not use IPs already assigned to other machines.

Check your node IP:

```bash
hostname -I
```

or:

```bash
kubectl get nodes -o wide
```

---

# Step 1 - Install MetalLB

Apply the official MetalLB manifest:

```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml
```

Verify pods:

```bash
kubectl get pods -n metallb-system
```

Expected:

```text
controller   Running
speaker      Running
```

---

# Step 2 - Configure IP Address Pool

Edit `ip-address-pool.yaml` and replace the IP range with your own unused IP range.

Example:

```yaml
addresses:
- 172.16.129.240-172.16.129.250
```

Apply:

```bash
kubectl apply -f ip-address-pool.yaml
```

---

# Step 3 - Configure L2 Advertisement

Apply:

```bash
kubectl apply -f l2-advertisement.yaml
```

## What is L2 Advertisement?

In simple words, MetalLB announces to the local network:

```text
I own this IP address.
Send traffic for this IP to me.
```

Example:

```text
MetalLB says: I own 172.16.129.240
```

Now your browser can access that IP.

---

# Step 4 - Deploy Test Nginx Application

Apply deployment:

```bash
kubectl apply -f nginx-deployment.yaml
```

Apply LoadBalancer service:

```bash
kubectl apply -f nginx-loadbalancer-service.yaml
```

Check:

```bash
kubectl get svc
```

Expected:

```text
NAME            TYPE           EXTERNAL-IP
nginx-lb        LoadBalancer   172.16.129.240
```

---

# Step 5 - Test from Browser

Open:

```text
http://<EXTERNAL-IP>
```

Example:

```text
http://172.16.129.240
```

Expected result:

```text
Welcome to nginx!
```

---

# MetalLB vs NodePort

## NodePort

```text
http://172.16.129.234:30080
```

You need node IP and port number.

## MetalLB LoadBalancer

```text
http://172.16.129.240
```

Cleaner and closer to real production.

---

# MetalLB with Ingress

MetalLB is very useful with Ingress.

Typical setup:

```text
Browser
   |
   v
MetalLB External IP
   |
   v
Traefik Ingress Controller
   |
   v
Applications
```

Example:

```text
hr.company.local      -> HR App
finance.company.local -> Finance App
```

---

# Troubleshooting

## Problem 1: EXTERNAL-IP is still pending

Check MetalLB pods:

```bash
kubectl get pods -n metallb-system
```

Check IP pool:

```bash
kubectl get ipaddresspool -n metallb-system
```

Check advertisement:

```bash
kubectl get l2advertisement -n metallb-system
```

---

## Problem 2: IP assigned but browser cannot access

Check if the IP is in the same network as your K3s node.

Check node IP:

```bash
hostname -I
```

Check service:

```bash
kubectl get svc nginx-lb
```

Check pods:

```bash
kubectl get pods -o wide
```

---

## Problem 3: IP conflict

Make sure your MetalLB IP range is not used by:

- Your router
- Your laptop
- Another VM
- Another server

Choose unused IPs only.

---

# Cleanup

Delete test app:

```bash
kubectl delete -f nginx-loadbalancer-service.yaml
kubectl delete -f nginx-deployment.yaml
```

Delete MetalLB config:

```bash
kubectl delete -f l2-advertisement.yaml
kubectl delete -f ip-address-pool.yaml
```

Optional: uninstall MetalLB:

```bash
kubectl delete -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml
```

---

# Student Deliverables

Students must submit screenshots of:

1. MetalLB pods running
2. IPAddressPool created
3. L2Advertisement created
4. LoadBalancer service showing External IP
5. Browser showing Nginx welcome page
6. Short explanation of why MetalLB is needed in local K3s
