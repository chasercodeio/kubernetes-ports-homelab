# [METUBE](https://github.com/alexta69/metube)

>Self-hosted YouTube downloader (web UI for youtube-dl / yt-dlp)

## Files needed
- [deployment.yaml](./deployment.yaml)
- [configmap.yaml](./configmap.yaml)
- [pvc.yaml](./pvc.yaml)
- [pv.yaml](./pv.yaml)
- [service.yaml](./service.yaml)
- [kustomization.yaml](./kustomization.yaml)

## How to Run

- Make sure to replace the path and node name in [pv.yaml](./pv.yaml)


```bash
kubectl apply -k .

# To stop deployment
kubectl delete -k .
```

