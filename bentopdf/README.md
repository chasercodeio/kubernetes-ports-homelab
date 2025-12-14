# [BentoPDF](https://github.com/alam00000/bentopdf)

>A Privacy First PDF Toolkit

## Files in this directory

- [deployment.yaml](./deployment.yaml)
- [service.yaml](./service.yaml)
- [ingress.yaml](./ingress.yaml) (Not currently being used)
- [kustomization.yaml](./kustomization.yaml)

## How to Run

Since all the actual PDF processing happens client browser side, we are just loading a website. To run:

```bash
# Assuming you are in this directory which has all the files
kubectl apply -k . 
```

This will deploy BentoPDF in the `homelab` namespace with ClusterIP networking.


## Known issues (probably my inexperience)

- Cannot get ingress working properly with all links inside the application routing to the correct location.
  - e.g: (IP/bentopdf/) works however when clicking on one of the many pdf manipulation tools, the request is (IP:/rest-of-request) not (IP/bentopdf/rest-of-request). 
  - The application docs do have a compiling option for this exact thing but that is aimed for compiling it yourself and hosting it rather than pulling a docker image.
