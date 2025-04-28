# Hedgedoc

## NOTES

- [Offical Docs](https://docs.hedgedoc.org/setup/docker/) Recommend using `quay.io/hedgedoc/hedgedoc` however, for some reason, kubernetes did not like pulling the image and starting it consistently. Thus, decided to use the linuxserver version, and the configmap uses linuxserver version of configuration environment variable style.


## DEPLOY

1. Change all the configuration options as needed using the `hedgedoc-configmap.yaml` 

2. Deploy (using kustomization)

```bash
kubectl apply -k .
```
