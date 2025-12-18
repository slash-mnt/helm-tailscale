# helm-tailscale

A Helm chart for deploying **Tailscale** into a Kubernetes cluster.

This chart allows you to run Tailscale components as Kubernetes workloads in order to securely connect your cluster, services, or workloads to a **Tailnet** (Tailscale network) without exposing them publicly.

Tailscale is a zero-configuration, WireGuard-based mesh VPN that simplifies secure networking between devices and services.

---

## Overview

The `helm-tailscale` chart provides a flexible and Kubernetes-native way to deploy Tailscale.  
It is designed to be:

- Simple to install and operate
- Configurable through standard Helm values
- Suitable for development, staging, and production environments
- Compatible with GitOps and CI/CD workflows

Typical use cases include:

- Connecting a Kubernetes cluster to a Tailnet
- Providing secure access to internal services
- Enabling private ingress or egress via Tailscale
- Running Tailscale nodes inside Kubernetes

---

## Prerequisites

- Kubernetes **1.24+** (recommended)
- Helm **3.x**
- A valid **Tailscale account**
- A Tailscale **auth key** (ephemeral or reusable, depending on your security model)

---

## Installation

### Clone the repository

```bash
git clone https://github.com/slash-mnt/helm-tailscale.git
cd helm-tailscale
```

### Install the chart

```bash
helm install tailscale ./ \
  --namespace tailscale \
  --create-namespace \
  -f values.yaml
```

You can also override values directly from the command line:

```bash
helm upgrade --install tailscale ./ \
  --namespace tailscale \
  --set tailscale.auth.key="tskey-xxxxx"
```

---

## Configuration

The chart is configured using `values.yaml`.

### Example configuration

```yaml
tailscale:
  auth:
    key: "tskey-xxxxx"

  image:
    repository: tailscale/tailscale
    tag: latest

  hostname: "k8s-node"

node:
  enabled: true
  extraTags:
    - "tag:kubernetes"

resources:
  requests:
    cpu: 50m
    memory: 64Mi
  limits:
    cpu: 200m
    memory: 256Mi
```

### Common values

| Key | Description | Default |
|----|------------|---------|
| `tailscale.auth.key` | Tailscale authentication key | `""` |
| `tailscale.image.repository` | Tailscale image repository | `tailscale/tailscale` |
| `tailscale.image.tag` | Image tag | `latest` |
| `tailscale.hostname` | Node hostname in Tailnet | `""` |
| `node.enabled` | Enable Tailscale node deployment | `true` |
| `node.extraTags` | Additional Tailnet tags | `[]` |

Refer to `values.yaml` for the full list of configurable options.

---

## Usage

Once installed, the Tailscale node should automatically appear in your **Tailscale admin console**.

From there, you can:

- Apply ACLs and tags
- Enable subnet routing (if configured)
- Use the node as a secure access point to cluster services

Depending on your configuration, this chart can be combined with:
- Kubernetes Services
- Ingress controllers
- Sidecar or gateway patterns

---

## Upgrading

To upgrade an existing release:

```bash
helm upgrade tailscale ./ \
  --namespace tailscale \
  -f values.yaml
```

---

## Uninstalling

To remove Tailscale from your cluster:

```bash
helm uninstall tailscale --namespace tailscale
```

---

## Security Considerations

- Prefer **ephemeral auth keys** when possible
- Use **Tailscale tags and ACLs** to restrict access
- Store auth keys in **Kubernetes Secrets** or external secret managers
- Review RBAC and PodSecurity policies in production environments

---

## Contributing

Contributions are welcome.

To contribute:

1. Fork the repository
2. Create a feature or fix branch
3. Commit your changes with clear messages
4. Open a Pull Request

Please ensure:
- Helm templates are linted
- Changes are documented
- Values remain backward-compatible when possible

---

## License

This project is licensed under the **MIT License**.  
See the `LICENSE` file for details.
