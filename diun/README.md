# DIUN - Notification for image updates (Docker & Kubernetes)

Note:

- Will require egress access as it will need to check registry versions.
  - Using Cilium or other CNI which allow for FQDN, can limit access to just dockerhub, github.

## USAGE

Learn more about Diun and configure it using [their site](https://crazymax.dev/diun/)

## Deploy

### Verify configuration

- Environment variables set in `diun-configmap.yaml`
- Path and Node for `pv.yaml`

### Apply config and run

```bash
kubectl apply -k .
```

### Teardown config and stop 

```bash
kubectl delete -k .
```
