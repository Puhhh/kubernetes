# Kubernetes Cluster Step By Step

## Talos OS Configure

### Control plane

```bash
talosctl gen secrets -o secrets.yaml
talosctl gen config --with-secrets secrets.yaml k8s-test https://172.168.101.100:6443
```

```bash
talosctl apply-config --insecure -n 172.168.101.100 --file controlplane.yaml
talosctl bootstrap -n 172.168.101.100 -e 172.168.101.100  --talosconfig=./talosconfig
talosctl kubeconfig -n 172.168.101.100 -e 172.168.101.100 --talosconfig=./talosconfig
```

### Workers

```bash
talosctl apply-config --insecure -n 172.168.101.101 --file worker.yaml --talosconfig=./talosconfig
talosctl apply-config --insecure -n 172.168.101.102 --file worker.yaml --talosconfig=./talosconfig
```

## Kubernetes Configure

[terraform-kubernetes-cilium](https://github.com/Puhhh/terraform-kubernetes-cilium)

[terraform-kubernetes-metallb](https://github.com/Puhhh/terraform-kubernetes-metallb)

[terraform-kubernetes-local-path-provisioner](https://github.com/Puhhh/terraform-kubernetes-local-path-provisioner)

[terraform-kubernetes-cert-manager](https://github.com/Puhhh/terraform-kubernetes-cert-manager)

[terraform-kubernetes-trust-manager](https://github.com/Puhhh/terraform-kubernetes-trust-manager)

[terraform-kubernetes-istio](https://github.com/Puhhh/terraform-kubernetes-istio)

[terraform-kubernetes-argocd](https://github.com/Puhhh/terraform-kubernetes-argocd)

[terraform-kubernetes-prometheus](https://github.com/Puhhh/terraform-kubernetes-prometheus)

[terraform-kubernetes-keycloak](https://github.com/Puhhh/terraform-kubernetes-keycloak)

[terraform-kubernetes-kiali](https://github.com/Puhhh/terraform-kubernetes-kiali)

---

[terragrunt-kubernetes](https://github.com/Puhhh/terragrunt-kubernetes)