# Kubernetes Cluster Step By Step

## Talos OS Configure

### Control plane

![Control plane](/images/talos-os/control-plane.png)

```bash
talosctl gen config k8s https://172.168.101.100:6433
talosctl -n 172.168.101.100 disks --insecure
vi controlplane.yaml
```
<details>
<summary>controlplane.yaml</summary>

```yaml
---
machine:
    install:
        disk: /dev/nvme0n1 # The disk used for installations.
    time:
        disabled: true
cluster:
  network:
    cni:
      name: none
  proxy:
    disabled: true
  discovery:
        enabled: true
        registries:
            kubernetes:
                disabled: true
            service:
                disabled: true
---
```
</details>

```bash
talosctl apply-config --insecure -n 172.168.101.100 --file controlplane.yaml
talosctl bootstrap -n 172.168.101.100 -e 172.168.101.100  --talosconfig=./talosconfig
talosctl kubeconfig -n 172.168.101.100 -e 172.168.101.100 --talosconfig=./talosconfig
```

### Workers

![Worker 1](/images/talos-os/worker-1.png) ![Worker 2](/images/talos-os/worker-2.png)

```bash
vi worker.yaml
```
<details>
<summary>worker.yaml</summary>

```yaml
---
machine:
    install:
        disk: /dev/nvme0n1 # The disk used for installations.
    time:
        disabled: true
cluster:
    discovery:
        enabled: true
        registries:
            kubernetes:
                disabled: true
            service:
                disabled: true
---
```
</details>

```bash
talosctl apply-config --insecure -n 172.168.101.101 --file worker.yaml
talosctl apply-config --insecure -n 172.168.101.102 --file worker.yaml
```

## Kubernetes Configure

[terraform-kubernetes-cilium](https://github.com/Puhhh/terraform-kubernetes-cilium)

[terraform-kubernetes-metallb](https://github.com/Puhhh/terraform-kubernetes-metallb)

[terraform-kubernetes-local-path-provisioner](https://github.com/Puhhh/terraform-kubernetes-local-path-provisioner)

[terraform-kubernetes-cert-manager](https://github.com/Puhhh/terraform-kubernetes-cert-manager)

[terraform-kubernetes-trust-manager](https://github.com/Puhhh/terraform-kubernetes-trust-manager)

[terraform-kubernetes-istio](https://github.com/Puhhh/terraform-kubernetes-istio)

[terraform-kubernetes-argocd](https://github.com/Puhhh/terraform-kubernetes-argocd)

[terraform-argocd-keycloak](https://github.com/Puhhh/terraform-argocd-keycloak)

[terraform-argocd-kiali](https://github.com/Puhhh/terraform-argocd-kiali)

---

[terragrunt-kubernetes](https://github.com/Puhhh/terragrunt-kubernetes)