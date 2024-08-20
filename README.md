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

### Cilium
* use [terraform-kubernetes-cilium](https://github.com/Puhhh/terraform-kubernetes-cilium)

### MetalLB
* use [terraform-kubernetes-metallb](https://github.com/Puhhh/terraform-kubernetes-metallb)

### Local Path Provisioner
* use [terraform-kubernetes-local-path-provisioner](https://github.com/Puhhh/terraform-kubernetes-local-path-provisioner)

### Cert Manager + Trust Manager
* use [terraform-kubernetes-cert-manager](https://github.com/Puhhh/terraform-kubernetes-cert-manager)
* use [terraform-kubernetes-trust-manager](https://github.com/Puhhh/terraform-kubernetes-trust-manager)

### Istio
* use [terraform-kubernetes-istio](https://github.com/Puhhh/terraform-kubernetes-istio)

### ArgoCD
* use [terraform-kubernetes-argocd](https://github.com/Puhhh/terraform-kubernetes-argocd)

### Keycloak
* use [terraform-argocd-keycloak](https://github.com/Puhhh/terraform-argocd-keycloak)

##### Kube API Auth
![client_1](/images/keycloak/client_1.png)
![client_2](/images/keycloak/client_2.png)
![client_3](/images/keycloak/client_3.png)
![client_4](/images/keycloak/client_4.png)
![client_5](/images/keycloak/client_5.png)
<details>
<summary>patch.yaml</summary>

```yaml
---
cluster:
  apiServer:
    extraArgs:
      oidc-issuer-url: https://keycloak.kubernetes.local/realms/kubernetes
      oidc-client-id: kube-api
      oidc-username-claim: email
      oidc-groups-claim: groups
---
```
</details>

```bash
talosctl patch mc --patch @patch.yaml -n 172.168.101.100 -e 172.168.101.100
```
```bash
kubectl oidc-login setup \
--oidc-issuer-url=https://keycloak.kubernetes.local/realms/kubernetes \
--oidc-client-id=kube-api \
--oidc-client-secret=E84xJLGVhJqFVg4IrmqvLmfRjqo2rkwj \
--insecure-skip-tls-verify=true
```
```bash
kubectl create clusterrolebinding oidc-cluster-admin --clusterrole=cluster-admin --user='https://keycloak.kubernetes.local/realms/kubernetes#124ce551-21ca-4bf8-8c11-03d89adab6b7'
```
```bash
kubectl config set-credentials oidc \
--exec-api-version=client.authentication.k8s.io/v1beta1 \
--exec-command=kubectl \
--exec-arg=oidc-login \
--exec-arg=get-token \
--exec-arg=--oidc-issuer-url=https://keycloak.kubernetes.local/realms/kubernetes \
--exec-arg=--oidc-client-id=kube-api \
--exec-arg=--oidc-client-secret=E84xJLGVhJqFVg4IrmqvLmfRjqo2rkwj \
--exec-arg=--insecure-skip-tls-verify
```
```bash
kubectl --user=oidc get nodes
```
```bash
kubectl config set-context --current --user=oidc
```