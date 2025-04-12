# OTEL eBPF profiler + OTEL collector + Pyroscope

[More Info](https://github.com/grafana/pyroscope/tree/main/examples/grafana-alloy-auto-instrumentation/ebpf-otel)

[More info 2](https://grafana.com/docs/pyroscope/latest/configure-client/opentelemetry/ebpf-profiler/)

**Make sure you start pyroscope and the OTEL collector before running the profiler

## Start pyroscope
Since the yamls are already provided in the directory, just need to apply it:

```bash
kubectl apply -k ./pyroscope/
```

## Start OTEL Collector
Since the yamls are already provided in the directory, just need to apply it:

```bash
kubectl apply -k ./collector/
```

## PROFILER

For Grafana's experimental OTEL pipeline, the profiler needs to be built and then deployed on kubernates.
The process for building the dockerfil and importing it to kubernates is shown in the following section.

### BUILD STEPS 

Most to all the articles and tutorials that explain how to use a dockerfile or docker image in kubernates require some sort of docker registry. Regardless of creating a private registry or using the docker registry, the process is too complicated for what needs to be achieved. An easier way exists, and will be outlined below.

**NOTE: I have created a simple bash script which does the following as well for ease of use. Called ./dockerToKubeImage**

**Make sure to run the following commands from current directory**

### Step 1: build the dockerfile as a docker image 
```bash
docker build -t test-ebpf-profiler:latest ./profiler/
```
- Note: the name and tag for the built image: test-ebpf-profiler:latest

### Step 2: Export / Save image as a .tar file

```bash
docker image save -o ./profiler/output.tar test-ebpf-profiler:latest 
```

### Step 3: Import the .tar file into kubernates

**This is slightly more complicated**

1. First you will need to know the default kubernates containerd runtime. This is important, by default, the command `ctr` (the containerd cli)  may not be using the same containerd socket as your kubernates distro for pulling images.

**For k3s: `/run/k3s/containerd/containerd.sock`**

2. Find the default namespace kubernates uses when pulling and storing images information. **You will need the socket path from above**

```bash
sudo ctr -a /run/k3s/containerd/containerd.sock ns list
```

**For k3s: `k8s.io`**

3. Now you can import the image to kubernates:

```bash
sudo ctr -a /run/k3s/containerd/containerd.sock -n k8s.io images import ./profiler/output.tar
```

4. Verify the image is in the kubernates registry:

```bash
sudo ctr -a /run/k3s/containerd/containerd.sock -n k8s.io images list | grep test
```

### Run the OTEL eBPF profiler kubernates manifest

```bash
kubectl apply -k ./profiler/
```

