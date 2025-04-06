# OtterWiki

_A minimalistic wiki powered by python, markdown and git._

## Files in dir:

- deployment.yaml
- pv.yaml
- pvc.yaml
- service.yaml
- kustomization.yaml
- README.md

## Setup

1. Change the **/your/path/goes/here** and **node** values in the **pv.yaml** file.

   1. **/your/path/goes/here**: The absolute location in your node (can be changed to path for network attached storage. More info in [nfs - volumes](https://kubernetes.io/docs/concepts/storage/volumes/#nfs))
   2. **node**: run `kubectl get nodes` to get the system you want to run the service on

2. Change the **type** field in **service.yaml** to **NodePort** (and uncomment the **nodePort** key/value pair) if you want to reach the service using either localhost or node ip:port. Other values for type:
   1. ClusterIP (default)
   2. NodePort
   3. ExternalName
   4. LoadBalancer

## Other configuration options

The [otterwiki configuration](https://otterwiki.com/Configuration) site provides all the configuration options for the service however it seems the best way of enabling these is through the settings inside the app. Otherwise, you can create a configmap with these values and pass them through to the **env** section of the deployment.

## To Run

```bash
kubectl apply -k .
```

- This will apply all the files in directory using kubernates kustomization

If using ClusterIP:

```bash
kubectl get pods -n homelab
```

- This will show you all the pods (in the homelab namespace) and their IP. Simply go to that address in the web browser to continue to otterwiki

## To stop and delete service

```bash
kubectl delete -k .
```

- This will teardown all the configs associated with otterwiki (including the persistent volume and volume claim). To just remove the pod:

```bash
kubectl delete -f deployment.yaml
```

## Notes to keep in mind

- Otterwiki requires at least **100Mi** of memory to function properly. This deployment sets a limit for 200Mi and 500m of CPU (1/2 a core).
