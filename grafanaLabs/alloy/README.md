# Grafana Alloy - The continous profiler 

## To Build

To build the Grafana Alloy, refer to the [pyroscope build](../pyroscope/README.md).

**Notice:**

If the namespace is set to default for some reason, you can quickly fix this by using:

```bash
# First make sure all the files have a namespace field 
grep -rl '^[[:space:]]*namespace:' . --include="*.yaml"

# Now search and replace all the values for namespace with "homelab" (only will edit files that have namespace field )
grep -rl '^[[:space:]]*namespace:' . --include="*.yaml" | tee modified_files.txt | xargs sed -i 's/^\( *namespace: *\).*/\1homelab/'
```

## Formatting the ConfigFile

By using the jsonnet and tk method, the configFile formatting is completely messed up. This is only a readability issue as kubernates has no problem reading the info in, however, if you need to edit the file and change some of the functions / add new metric processing information, it becomes impossible. The quickest way to fix this is the following:

1. Apply the ConfigFile

```bash
kubectl apply -f ./v1.ConfigMap-alloy-config-pyroscope.yaml
```

2. Describe the configFile to get the proper formatted information (it is quite long)

```bash
# get the name of the configmap in the namespace homelab
kubectl get configmaps -n homelab

# Now describe and output to file cos its massive
kubectl describe configmap alloy-config-pyroscope -n homelab > temp_configmap_info.txt
```

3. Create a new configmap with the same details.. The default has been provided in this file 

## To Run

```bash
kubectl apply -k .
```

Note: Make sure you are in the same directory path as the yaml files

### 5. Stop and remove Grafana Alloy

```bash
kubectl delete -k .
```

