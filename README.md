# Kubernetes Ports of popular Homelab Services

> Please keep in mind that I am still learning Kubernetes and the resources it has. For each of these, please make sure to DYOR and enforce the actions you want in your own environment.

## Working Services
1. [Otterwiki - Minimalistic wiki powered by python, markdown and git](./otterwiki/README.md)
2. [HomePage - Modern, fully static, highly customizable application dashboard](./homepage/README.md)
3. [Glance - All your feeds in one place, highly customizable dashboard](./glance/README.md)
4. [Ntfy - "Notify", Send push notifications to your phone](./ntfy/README.md)
5. [Keycloak - OSS Identity and Access Management. Useful for Auth](./keycloak/README.md)
6. [Outline Wiki - Team knowledge base for internal documentation](./outline/)
7. [GrafanaLabs Grafana](./grafanaLabs/README.md)
8. [GrafanaLabs Pyroscope UI *Moving*](./grafanaLabs/README.md)
9. [GrafanaLabs Alloy *Moving*](./grafanaLabs/README.md)
10. [Mealie - Easy to use recipe management app](./mealie/)
11. [ConvertX - File converter, supports 1000+ formats](./convertx/)
12. [Kavita - Fast and rich cross platform reading server](./kavita/)
13. [Grist - Modern relational Spreadsheet](./grist/)
14. [Stirling-PDF - Perform various operations on PDFs](./stirling-pdf/)
15. [Diun - Docker Image Update Notifier (Working for kubernetes)](./diun/)
16. [Penpot - Design & collaboration tool](./penpot/README.md)
17. [Hedgedoc - Collaborative markdown editor](./hedgedoc/README.md)
18. [Trilium Notes - Personal knowledge base](./trilium/README.md)


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
Having a _Global_ Network Policy allows for more control over the network traffic in the cluster. 
[For more info on Kubernetes Network Policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

**[I have now moved to using Cilium as my container networking interface and all network policies are in CiliumNetworkPolicy and CiliumClusterwideNetworkPolicy. This is better explained in the pre-config folder](./pre-config/README.md)**

## TODO:
- VictoriaMetrics
- VictoriaLogs
- HEAPS more ...

## Network "Roles" for current Pods

### None

- ConvertX
- Grist
- HomePage
- Kavita ( Initially does require to download some stuff, but once setup, not needed)
- Keycloak
- Ntfy
- Otterwiki
- Outline


### egress-allow
- Glance
- Mealie
- StirlingPDF-{lite, full, fat} (For now)
