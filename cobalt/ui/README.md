# Cobalt-UI

## Building and deploying the UI (from Dockerfile to kube deployment)

> To have a look at how to run the UI locally, check out the [docker readme](../docker/README.md).

> I created a util script called [dockerToKubeImage](../../utils/dockerToKubeImage) which greatly eases the pain of exporting a docker image from dockerfile to local kubernetes image repository without going through the tedious process of creating a dockerhub account and pulling from there. For more information on this tool, have a look at the [utils readme](../../utils/README.md) folder.

### Preliminary steps

Before using the script, check your kubernetes containerd socket (where kuberenetes keeps its images once it gets pulled from dockerhub or anywhere else).

#### Check kubernetes containerd socket

```bash
# using default ns with ctr, it shows moby (this is not the socket we want to target)
sudo ctr ns list

# If this shows k8s.io, it is probably right
sudo ctr -a /run/k3s/containerd/containerd.sock ns list
```

### Using the script

```bash
# use "../../utils/dockerToKubeImage -h" to get all the options

# The command for use to use (in this directory which has the dockerfile)
../../utils/dockerToKubeImage -p ./ -d cobalt-ui:latest -r
```

### Running the Ui in kubernetes

Now that we have the image:tag in the local kubernetes image repo, we can pull it and run it.

```bash
# make sure the details match your circumstances.
kubectl apply -k .
```

#### See the image in the k3s local images repo

```bash
sudo ctr -a /run/k3s/containerd/containerd.sock -n k8s.io images list | grep cobalt
```

##### To remove the image from k3s images repo

```bash
sudo ctr -a /run/k3s/containerd/containerd.sock -n k8s.io images rm docker.io/library/cobalt-ui:latest
```

##### To prune all unused k3s images

```bash
sudo ctr -a /run/k3s/containerd/containerd.sock -n k8s.io images prune --all
```
