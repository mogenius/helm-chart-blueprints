# Helm Chart Blueprints

Pre-configured Helm chart blueprints for the [mogenius](https://mogenius.com) platform. Blueprints appear as quick-start cards in the Helm install flow — selecting one auto-adds the repository and pre-fills chart name, version, namespace, release name, and default values.

---

## Available Blueprints

| Blueprint | Category | Chart |
|---|---|---|
| cert-manager | Security | `jetstack/cert-manager` |
| Traefik | Networking | `traefik/traefik` |
| MetalLB | Networking | `metallb/metallb` |
| Cilium | Networking | `cilium/cilium` |
| Calico | Networking | `projectcalico/tigera-operator` |
| Alertmanager | Monitoring | `prometheus-community/alertmanager` |
| Kube Prometheus Stack | Monitoring | `prometheus-community/kube-prometheus-stack` |
| Argo CD | GitOps | `argo/argo-cd` |
| Flux Operator | GitOps | `flux-operator/flux-operator` |
| Metrics Server | Operations | `metrics-server/metrics-server` |
| External Secrets Operator | Security | `external-secrets/external-secrets` |
| Harbor | Registry | `harbor/harbor` |
| Kyverno | Security | `kyverno/kyverno` |
| NFS Subdir External Provisioner | Storage | `nfs-subdir-external-provisioner/...` |
| Renovate | Operations | `renovate/renovate` |

---

## Repository Structure

```
helm-chart-blueprints/
├── index.yaml          # Blueprint list (fetched by the UI for the selector)
└── charts/
    ├── cert-manager.yaml
    ├── traefik.yaml
    └── ...
```

### `index.yaml`

A short metadata list of all available blueprints — fetched once to populate the selector UI.

### `charts/{id}.yaml`

Full blueprint definition fetched when a user selects a blueprint. Format:

```yaml
apiVersion: v1
kind: HelmBlueprint
metadata:
  id: cert-manager
  displayName: cert-manager
  description: Automated TLS certificate management for Kubernetes
  category: security
  icon: cert-manager
  tags: [tls, certificates, letsencrypt, security]
spec:
  repository:
    name: jetstack
    url: https://charts.jetstack.io
  chart:
    name: jetstack/cert-manager
    version: "v1.17.1"
  install:
    release: cert-manager
    namespace: cert-manager
  values: |
    crds:
      enabled: true
```

---

## Custom Blueprint Repository

Organizations can host their own private blueprint repository and configure it in the mogenius organization settings under **Helm Blueprints**. Private repos are supported via an access token.

## Contributing

To add or update a blueprint, open a PR that adds or modifies a file under `charts/` and updates `index.yaml` accordingly.
