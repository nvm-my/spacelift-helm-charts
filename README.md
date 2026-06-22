
# Spacelift Helm Charts

Helm charts for deploying Spacelift components on Kubernetes.

This repository contains charts for several Spacelift-related services, including worker pools, self-hosted components, flows, and VCS agents. Each chart lives in its own directory and includes chart-specific documentation and configuration.

## Repository Contents

| Chart | Description |
|---|---|
| `spacelift-worker-pool` | Deploys a Spacelift private worker pool to Kubernetes. |
| `spacelift-self-hosted` | Deploys Spacelift self-hosted components. |
| `spacelift-flows` | Deploys Spacelift Flows on Kubernetes. |
| `spacelift-flows-agent` | Deploys a Spacelift Flows agent pool. |
| `spacelift-promex` | Chart for Spacelift Promex-related deployment. |
| `spacelift-workerpool-controller` | Controller chart related to Spacelift worker pool operations. |
| `vcs-agent` | Deploys the Spacelift VCS agent. |

## Prerequisites

- Kubernetes 1.19+ recommended
- Helm 3.x
- Access to the relevant Spacelift environment and credentials
- Kubernetes permissions to create namespaces, secrets, deployments, services, and other chart-managed resources

## Getting Started

1. Clone this repository:

```bash
git clone https://github.com/nvm-my/spacelift-helm-charts.git
cd spacelift-helm-charts
```

2. Pick the chart you want to deploy.

3. Review the chart-specific README and default values:

- [spacelift-worker-pool/README.md](spacelift-worker-pool/README.md)
- [spacelift-self-hosted/README.md](spacelift-self-hosted/README.md)
- [spacelift-flows/README.md](spacelift-flows/README.md)
- [spacelift-flows-agent/README.md](spacelift-flows-agent/README.md)
- [vcs-agent/README.md](vcs-agent/README.md)

4. Customize the chart values for your environment.

5. Install the chart with Helm:

```bash
helm upgrade --install <release-name> ./<chart-directory> -f <values-file>
```

Example:

```bash
helm upgrade --install spacelift-worker-pool ./spacelift-worker-pool -f values.yaml
```

## Chart Structure

Most charts follow a standard Helm layout:

- `Chart.yaml` — chart metadata
- `values.yaml` — default configuration values
- `values.schema.json` — optional values schema
- `templates/` — Kubernetes manifest templates
- `README.md` — chart-specific usage instructions

## Documentation

Detailed instructions are maintained inside each chart directory. Start there for:

- installation steps
- required secrets
- environment-specific configuration
- resource tuning
- ingress and networking settings
- troubleshooting guidance

You can also refer directly to the chart documentation:

- [Worker Pool chart documentation](spacelift-worker-pool/README.md)
- [Self-hosted chart documentation](spacelift-self-hosted/README.md)
- [Flows chart documentation](spacelift-flows/README.md)
- [Flows Agent chart documentation](spacelift-flows-agent/README.md)
- [VCS Agent chart documentation](vcs-agent/README.md)

## Development

### Validate a Chart

Run standard Helm validation commands while working on a chart:

```bash
helm lint ./<chart-directory>
helm template <release-name> ./<chart-directory> -f <values-file>
```

Example:

```bash
helm lint ./spacelift-worker-pool
helm template spacelift-worker-pool ./spacelift-worker-pool -f ./spacelift-worker-pool/values.yaml
```

### Publish Charts

To publish a new chart version, create a Git tag in the format:

```bash
v<version>
```

Example:

```bash
v0.0.1
```

Pushing a release tag triggers the publishing workflow.  
If your repository automation is configured like the current setup, multiple charts may be published together and share the same version tag.

## Notes

- This repository appears to be a fork of the upstream Spacelift Helm charts repository.
- Some charts may be more complete than others, so always check the chart-local `README.md` before deploying.
- Secrets and sensitive runtime configuration should be provided through Kubernetes secrets or external secret management, not committed directly to Git.

## License

This repository is licensed under the MIT License. See `LICENSE` for details.
