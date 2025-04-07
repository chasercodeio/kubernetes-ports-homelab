# NTFY

[github](https://github.com/binwiederhier/ntfy)

## Files in Dir

- configmap.yaml
- deployment.yaml
- kustomization.yaml
- pv.yaml
- pvc.yaml
- service.yaml
- README.md

## Setup

- [NTFY Configuration](https://docs.ntfy.sh/config/)

1. Set the configuration to the `configmap.yaml`

2. Set your absoulute local cache path (any dir) to the `pv.yaml`

3. Set your node name to the `pv.yaml`

4. Change the `service.yaml` to suit your needs ("NodePort", "ClusterIP")


## To Run

```bash
kubectl apply -k .
```

- This will apply all the files in directory using kubernates kustomization

If using ClusterIP:

```bash
kubectl get pods -n homelab
```

- This will show you all the pods (in the homelab namespace) and the IP. Simply go to that address in the web browser to continue to ntfy

## To stop and delete service

```bash
kubectl delete -k .
```

- This will teardown all the configs associated with ntfy (including the persistent volume and volume claim). To just remove the pod:

```bash
kubectl delete -f deployment.yaml
```

