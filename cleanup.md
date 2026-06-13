# Cleanup Commands

Delete the full lab:

```bash
kubectl delete namespace networking-lab
```

Verify:

```bash
kubectl get ns
kubectl get all -n networking-lab
```

If the namespace is deleted, all resources inside it are removed.
