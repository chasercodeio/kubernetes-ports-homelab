# Network Policies

This directory has all the files needed to secure all namespaces and apply ingress / egress traffic rules. 

## Folders in directory

1. [default_kubernetes](./default_kubernates/) | Old way of NetworkPolicy
2. [Cilium Networking](./cilium_nw/) | New NetworkPolicy (Using Cilium CNI)

## New (Using Cilium CNI)

### Motivation

Decided to install Cilium Networking as my container networking interface as it offers a lot more advanced features and more control over exactly what I want happening with the networking in this cluster. 

- NetworkPolicy encompassing all namespaces (CiliumClusterwideNetworkPolicy (CCNP))
  - Useful for an overall default deny and only allow with roles
- Ability to restrict Ingress and Egress to none, pod-to-pod (within cluster), local area network, all (world)
  - Specifically able to specify CIDR and FQDN
- Much better network visibility and observability

### Curently Active Network Policies (NPs)

- All NPs are CiliumClusterwideNetworkPolicy (CCNP) as I want to only define them once and used regardless of namespace

- [Top level Deny all policy](./cilium_nw/deny_all_namespace.yaml)
  - Only allow pods to communicate with the kube-dns and nothing else (including other pods, lan, world)
  - This enforces network isolation for pods that really do not need network access (Almost all pods will require ingress setting but better to actively add that role than assume)
- [Roles based CCNPs](./cilium_nw/roles/)
  - Allow for fine-grain configuration of which pods need which network policy.
  - Roles / Labels to use for pods:
    - [egress: all](./cilium_nw/roles/allow_all_egress.yaml)
    - [egress: lan](./cilium_nw/roles/allow_lan_egress.yaml)
    - [ingress: all](./cilium_nw/roles/allow_all_ingress.yaml)
    - [ingress: lan](./cilium_nw/roles/allow_lan_ingress.yaml)
    - [egress: allow_kubeapi_server](./cilium_nw/roles/allow_pods_kubeapi_bidir.yaml)
    - (TODO: maybe add roles for just pod-to-pod and not localhost?)
    - (With above, maybe also add one for node only (localhost, pod-to-pod))

- Last level CCNPs
  - This will be created and used alongside the service and will target only the pod / deployment. Can be either a CiliumClusterwideNetworkPolicy or just a CiliumNetworkPolicy (CNP)
  - This last layer is to add that extra fine grain configuration (if required). Example would be something like the service DIUN, which requires egress access to just the registries. Easiest approach here would be to create a seperate CNP with FQDN whitelisting for just those endpoints.

### DEPLOY CCNPs

```bash
kubectl apply -f ./cilium_nw/deny_all_namespace.yaml
kubectl apply -f ./cilium_nw/roles/ 
```

### USAGE for PODS

- Make sure the labels of your choosing are present in the pod deployment (TODO: update all manifests to include this new change)

- **For ingress Policies that are LAN, default cilium will use encapsulation which obscure the incoming IPs of packets, not allowing the CCNP to act properly. To fix this:**
  - Make sure each service.yaml in the services you want to host has `externalTrafficPolicy: Local` option added under the spec. E.G:
```yaml
...
type: NodePort / Loadbalancer
externalTrafficPolicy: Local
Ports:
  - ...
```

