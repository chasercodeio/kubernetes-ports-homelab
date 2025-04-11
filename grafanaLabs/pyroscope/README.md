# Pyroscope

## BUILD
For both Pyroscope and Pyroscope Alloy, the [official instructions](https://grafana.com/docs/pyroscope/latest/deploy-kubernetes/tanka-jsonnet/) recommend either using helm to deploy it or building it using jsonnet and tanka. The following instructions are for latter (with a few changes). 

### 1. Install dependencies

- Install [Tanka](https://tanka.dev/install) and [Jsonnet](https://github.com/jsonnet-bundler/jsonnet-bundler):
```bash
# The following command is if you have Golang already installed
# make sure to be outside of GOPATH or a go.mod project
go install github.com/grafana/tanka/cmd/tk@latest
go install github.com/jsonnet-bundler/jsonnet-bundler/cmd/jb@latest
```

**If you cannot use `tk` or `jb` in the terminal, you will need to make sure your path includes go's bin**

### 2. Setup the Jsonnet project

1. Initialise Tanka

```bash
# Initialize a Tanka directory
mkdir jsonnet-example && cd jsonnet-example

tk init --k8s=1.21
```

2. Install the Pyroscope jsonnet library

```bash
# Install Pyroscope jsonnet
jb install github.com/grafana/pyroscope/operations/pyroscope@main
```

3. Install the required Tanka Utils

```bash
# Install required tanka-util
jb install github.com/grafana/jsonnet-libs/tanka-util@master
```

4. Setup your current cluster as the server for the default environment

```bash
# Setup your current cluster as the server for the default environment
tk env set environments/default --server-from-context=$(kubectl config current-context)
```

### 3. Configuring Pyroscope in either Monolithic or microservices mode

Decided to run it as Monolithic but it should not be too different. Check out the official instructions for running it in miscroservice mode.

1. Create Default main.jsonnet file:
*Make sure This file is in the following dir:* `environments/default/main.jsonnet`

**Changed from default namespace to "homelab"**

```jsonnet
local pyroscope = import 'pyroscope/jsonnet/pyroscope/pyroscope.libsonnet';
local tk = import 'tk';

pyroscope.new(overrides={
  namespace: "homelab",
})
```

2. Generate the kubernates YAML manifests and store them in the `./manifests` directory

**Make sure you are in the .../jsonnet-example directory** 

```bash
# Take a look at the generated YAML manifests.
tk show environments/default

# Export the YAML manifests to the folder `./manifests`:
tk export ./manifests environments/default
```

3. Split the created YAML manifests into pyroscope and pyroscope alloy

   1. Create / move the files relevant to just pyroscope to the proper pyroscope folder `../../` <- (from ./manifests)
   2. Create / Move the rest of the files to a pyroscope-Alloy folder (Seperate service?) `../../alloy`
   3. **ConfigMap for Alloy** is not formatted well, to fix this, see the [Pyroscope Alloy](../alloy/) directory.

**Seems like the namespaces did not change some files even with the changes. To amend the namespace, use this search and replace command from the directory with the files:**

```bash
# First make sure all the files have a namespace field 
grep -rl '^[[:space:]]*namespace:' . --include="*.yaml"

# Now search and replace all the values for namespace with "homelab" (only will edit files that have namespace field )
grep -rl '^[[:space:]]*namespace:' . --include="*.yaml" | tee modified_files.txt | xargs sed -i 's/^\( *namespace: *\).*/\1homelab/'
```

4. Create a Kustomization.yaml file for both folders (Just focusing on Pyroscope for now)

**It should look something like this:**

```bash
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: homelab
resources:
  - v1.Service-pyroscope.yaml
  - v1.Service-pyroscope-memberlist.yaml
  - v1.Service-pyroscope-headless.yaml
  - v1.ServiceAccount-pyroscope.yaml
  - v1.ConfigMap-pyroscope-config.yaml
  - v1.ConfigMap-pyroscope-overrides-config.yaml
  - policy-v1.PodDisruptionBudget-pyroscope.yaml
  - apps-v1.StatefulSet-pyroscope.yaml
```

### 4. Run Pyroscope UI

```bash
kubectl apply -k .
```

Note: Make sure you are in the same directory path as the yaml files

### 5. Stop and remove Pyroscope UI

```bash
kubectl delete -k .
```

## This folder is just for the Pyroscope UI, for the full stack (UI + Alloy), check out the [Alloy Folder](../alloy/)
