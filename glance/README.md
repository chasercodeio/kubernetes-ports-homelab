# GLANCE

[What if you could see everything at a...](https://github.com/glanceapp/glance)

## Files in Dir
- default-configmap.yaml
- service.yaml
- deployment.yaml
- kustomization.yaml
- README.md

## Setup
For ease of backups and to remove filesystem dependency using volumes, a configmap is provided.

1. Customise the configmap, keeping in mind that the values given are the defaults
  
2. To add more pages, add another section in the configmap with the filename for that page and fill it out following a similar structure to the rest.
  - Edit the deployment->volumes section with the key being the name of the file in the configmap created and the path being the name you want it to be called inside the volume.
  
3. To make the service work using "localhost" or the node IP, change the "type" value to NodePort in `service.yaml` and add the nodePort value to a number higher than 30000.

## Other Configuration options

[Glance github on configuration](https://github.com/glanceapp/glance/blob/main/docs/configuration.md)

## To Run

```bash
kubectl apply -k .
```

- This will apply all the files in directory using kubernates kustomization

If using ClusterIP:

```bash
kubectl get pods -n homelab
```

- This will show you all the pods (in the homelab namespace) and their IP. Simply go to that address in the web browser to continue to glance

If using NodePort:

Simply go to the node_IP:8080 in the browser and watch Glance work!

## To stop and delete service

```bash
kubectl delete -k .
```

- This will teardown all the configs associated with glance. To just remove the pod:

```bash
kubectl delete -f deployment.yaml
```

## Notes to keep in mind
- I could not get the site to notice the changes made in the configmap after applying it, so it seems for me, the auto reload feature does not work. You will need to apply the configmap and then delete the "pod" (or restart the deployment):
```bash
kubectl delete -f deployment.yaml
kubectl apply -f deployment.yaml
```
- Glance is an application which requires EGRESS (ability to communicate out). For this to work, you will need to add the `egress-allow` role (I have already added it). This is because, when you use the [pre-config](../pre-config/) files given, it will by default, stop all egress networking except for pods that have that role.
