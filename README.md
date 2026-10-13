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
| Renovate Operator | Operations | `mogenius/renovate-operator` |
| Podinfo | Demo | `podinfo/podinfo` |

---

## Retired Blueprints

| Blueprint | Retired | Replacement |
|---|---|---|
| Agent Sandbox | 2026-10 | PlatformConfig component `agentSandboxes` |

### Agent Sandbox → `agentSandboxes` PlatformConfig component

The standalone **Agent Sandbox** blueprint was removed. Agent sandboxes are now a
first-class platform component, enabled per cluster under **Cluster Settings →
Platform Config** (the `agentSandboxes` component). The operator delivers the same
`mogenius/mogenius-agent-sandbox` chart through GitOps instead of a one-off
`helm install`.

**Prerequisite:** the component is only delivered on clusters running a GitOps
engine (Argo CD or Flux). A cluster without one cannot enable it.

**Migrating a cluster that used the blueprint:** the component installs the *same*
Helm release (`agent-sandbox`) into the *same* namespace (`agent-sandbox-system`)
as the old blueprint, but via an Argo CD `Application` rather than a direct
`helm install`. Leaving the old release in place means two owners for the same
resources. Uninstall the blueprint release first, then enable the component:

```sh
helm uninstall agent-sandbox -n agent-sandbox-system
# then enable the `agentSandboxes` component in Cluster Settings → Platform Config
```

The sandbox namespace, the default SandboxTemplate, the warm pool and the
per-sandbox NetworkPolicies are recreated by the component.

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
    version: "v1.20.2"
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
