# [Homepage](https://gethomepage.dev/)

## Files in Dir

- README.md
- configmap.yaml
- deployment-w-configmap.yaml
- deployment-w-pv_pvc.yaml
- pv.yaml
- pvc.yaml
- service.yaml
- kustomization.yaml

## Setup

Choose between using the configmap or using a persistent volume / persistent volume claim for config data

### For configmap:

1. Edit kustomization.yaml to look like the following:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: homelab
resources:
  - service.yaml
  - configmap.yaml
  - deployment-w-configmap.yaml
```

- This will ensure only the files associated with running the configmap deployment are applied

2. Populate the **HOMEPAGE_ALLOWED_HOSTS** env in **deployment-w-configmap.yaml** to allow the IPs of the hosts you want to connect to the homepage service. I have left it to "\*" which allows ALL.

3. Add all the services you want on your homepage to the configmap. I have left the defaults.

4. Edit the **service.yaml** to allow for joining via localhost (if needed) Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: homepage-svc
  namespace: homelab
  labels:
    name: homepage-svc
  annotations:
    name: homepage-svc
spec:
  type: NodePort # This allows for connection via node IP or localhost (if hosted on same device)
  ports:
    - port: 3000
      name: http
      targetPort: http
      protocol: TCP
      nodePort: 30000 # Must be >= 30000
  selector:
    name: homepage
```

### For PV / PVC:

1. Edit kustomization.yaml to look like the following:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: homelab
resources:
  - pv.yaml
  - pvc.yaml
  - service.yaml
  - deployment-w-pv_pvc.yaml
```

- This ensures the persistent volume and the pods claim are both also applied when applying the rest of the configs

2. Populate the **HOMEPAGE_ALLOWED_HOSTS** env in **deployment-w-configmap.yaml** to allow the IPs of the hosts you want to connect to the homepage service. I have left it to "\*" which allows ALL.

3. Download the default configs required for the service [here](https://github.com/gethomepage/homepage/tree/dev/docs/configs). Edit the files to suit your configuration

4. Use the path of the config (make sure it is absolute) in the **pv.yaml** under the section on local->path. Also make sure to populate the Node variable at the end of that file to your node. Find the name using:

```bash
kubectl get nodes
```

5. Check the **service.yaml** and apply as specified in step 4 of the configmap setup above (if ClusertIP is not good for your setup).

## To Run

After following the setup section, to run the configs:

```bash
kubectl apply -k .
```

- This will apply the kubernates manifests based on the kustomization yaml.

### To access the homepage dashboard:

#### If using ClusterIP, then find the pod IP using:

```bash
kubectl get pods -n homelab -o wide
```

Then go to that IP:3000 (Port is 3000 in service.yaml)

#### If using NodePort:

Connect to [localhost:30000](http://localhost:30000) (If using 30000 as the port number in **service.yaml**)

## To Stop and delete service

```bash
kubectl delete -k .
```

- This will teardown all the configs associated with homepage (including the persistent volume and volume claim).

## Notes to keep in mind

- The service seems to require atleast 210m of CPU and 115Mi of memory (initially). You may experience issues loading into the homepage dashboard with any amount less of these two.

- After configuring the services to monitor and be accessable in the configmap, you will need to restart the pod to load in the new values.

- ConfigMaps seem to be the best practice for loading in values for kubernates as long as its small data and not frequently changed (Perfect usecase for a dashboard / homepage ) ... It is also easy to version control and store backups
