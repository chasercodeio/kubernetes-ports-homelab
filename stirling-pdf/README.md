# StirlingPDF (lite, full, fat)

## Issues / Not Ideals:

**Requires the role: egress-allow** every run to install a font package....

- Needs:
  - font-noto
```bash
# $ k apply -k .
# $ k logs stirling-pdf-pod -n homelab -f
Running Stirling PDF with DOCKER_ENABLE_SECURITY=false and VERSION_TAG=0.45.6
Installing font package: font-noto
fetch https://dl-cdn.alpinelinux.org/alpine/v3.21/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.21/community/x86_64/APKINDEX.tar.gz
WARNING: fetching https://dl-cdn.alpinelinux.org/alpine/v3.21/main: Permission denied
WARNING: fetching https://dl-cdn.alpinelinux.org/alpine/v3.21/community: Permission denied
fetch https://dl-cdn.alpinelinux.org/alpine/edge/main/x86_64/APKINDEX.tar.gz
WARNING: fetching https://dl-cdn.alpinelinux.org/alpine/edge/main: Permission denied
fetch https://dl-cdn.alpinelinux.org/alpine/edge/community/x86_64/APKINDEX.tar.gz
WARNING: fetching https://dl-cdn.alpinelinux.org/alpine/edge/community: Permission denied
fetch https://dl-cdn.alpinelinux.org/alpine/edge/testing/x86_64/APKINDEX.tar.gz
```
- ^ Permission denied due to networkpolicy? (default deny)

Work-Arounds:
- Can leave egress-allow role enabled, (not ideal)
- download the dockerfile and add the font as part of the install package... that way no egress is needed
- (If using Cilium as the CNI or Calico plus): Add custom networkpolicy to allow FQDN to alpinelinux cdn

## Deploy (With role: egress-allow)

1. Make sure the path and node name for the `pv.yaml` reflects your desired location and setup
2. Make sure the image for stirling-pdf is the version you desire (ultra-lite, full, fat)
3. (optional) Make sure the image tag is a number and not set to latest (do manual upgrades)(best practice)
4. Ensure the service type and port in `service.yaml` is as you want. For localhost:port connectivity, ensure the service type is set to NodePort and not ClusterIP

### Deploy

```bash
kubectl apply -k .
```

### Stop and remove container

```bash
kubectl delete -k .
```
