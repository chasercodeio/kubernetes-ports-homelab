# Utils

This directory contains some important scripts for the homelab. The following will outline each one and what they help with.

1. [dockerToKubeImage](./dockerToKubeImage)

- This script will help package a local docker image (from dockerfile) and import it to your local kubernetes image repository. More info on the commands below

## 1. dockerToKubeImage

The following commands are equivalent to running the script.

**Make sure to run the following commands from current directory**

### Step 1: build the dockerfile as a docker image

```bash
docker build -t image:tag ./path/
```

- Note: the name and tag for the built image

### Step 2: Export / Save image as a .tar file

```bash
docker image save -o ./path/output.tar image:tag
```

### Step 3: Import the .tar file into kubernates

**This is slightly more complicated**

1. First you will need to know the default kubernates containerd runtime. This is important, by default, the command `ctr` (the containerd cli) may not be using the same containerd socket as your kubernates distro for pulling images.

**For k3s: `/run/k3s/containerd/containerd.sock`**

2. Find the default namespace kubernates uses when pulling and storing images information. **You will need the socket path from above**

```bash
sudo ctr -a /run/k3s/containerd/containerd.sock ns list
```

**For k3s: `k8s.io`**

3. Now you can import the image to kubernates:

```bash
sudo ctr -a /run/k3s/containerd/containerd.sock -n k8s.io images import ./path/output.tar
```

4. Verify the image is in the kubernates registry:

```bash
sudo ctr -a /run/k3s/containerd/containerd.sock -n k8s.io images list | grep image
```
