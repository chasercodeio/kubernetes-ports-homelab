# Kubernetes Ports of popular Homelab Services

> Please keep in mind that I am still learning Kubernetes and the resources it has. For each of these, please make sure to DYOR and enforce the actions you want in your own environment.

## Pre-Service configuration

Before applying each service, a few files need to be applied (These are all found in the [pre-config]() folder.
**Namespace:**
All above services use _homelab_ as the namespace, to replicate this in your setup,create the following yaml:

```yaml filename="homelab-namespace.yaml"
apiVersion: v1
kind: Namespace
metadata:
  name: homelab
```

Make sure to apply this before going to the next pre-service file

```bash
kubectl apply -f homelab-namespace.yaml
```

---

**Network Policy:**
Having a _Global_ Network Policy allows for more control over the network traffic in the cluster. In this case, this will apply traffic control in between pods and the outside world (only for the homelab namespace).
We will also be creating an _exception_ role which we can apply for services that require it.
In this case, I want a global **block** for both Ingress and Egress to the outside world (Pods cannot receive data and cannot transmit data). I still want pods to be able to communicate locally and to each other.
[For more info on Kubernetes Network Policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
In my case, this will be done in 2 files, the first being:

```yaml filename="homelab-nw-policy.yaml"
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: homelab-nw-policy
  namespace: homelab
spec:
  podSelector: {} # Apply to all pods in the namespace
  policyTypes:
    - Egress # Outbound traffic
    - Ingress # Inbound traffic
  egress:
    - to:
        - ipBlock:
            cidr: 10.0.0.0/8 # Allow internal cluster communication (modify based on your cluster's CIDR)
        - ipBlock:
            cidr: 192.168.0.0/16 # Allow internal communication (modify based on your lan ip CIDR)
  ingress:
    - from:
        - ipBlock:
            cidr: 192.168.0.0/16 # Allow internal communication
        - ipBlock:
            cidr: 10.0.0.0/8 # Allow internal cluster communication
```

The second which is the _Exceptions for roles_:

```yaml filename="homelab-nw-ex-policy.yaml"
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: homelab-nw-policy-allow-egress
  namespace: homelab
spec:
  podSelector:
    matchLabels:
      role: egress-allow
  policyTypes:
    - Egress
  egress:
    - {}
```

Make sure to apply these two yamls to have the network policies enforced

```bash
kubectl apply -f homelab-nw-policy.yaml
kubectl apply -f homelab-nw-ex-policy.yaml
```

## Working Services
1. [Otterwiki](./otterwiki/README.md)
2. [HomePage](./homepage/README.md)
