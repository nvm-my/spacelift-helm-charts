This repository is a **public fork of Spacelift’s Helm charts repo** focused on deploying multiple **Spacelift-related Kubernetes components via Helm**. It is active, uses **`main`** as the default branch, is licensed under **MIT**, and its latest visible push was on **June 18, 2026**. The repo has **no releases**, **no open issues**, and appears to be maintained mainly through direct commits/PR merges from the upstream project. It is also explicitly marked as a **fork** of `spacelift-io/spacelift-helm-charts`.  

## Executive summary

- **Repo:** `nvm-my/spacelift-helm-charts`
- **Type:** Public fork
- **Primary purpose:** Helm charts for deploying Spacelift-related services on Kubernetes
- **Default branch:** `main`
- **License:** MIT
- **Main content type:** Go-template-based Helm charts
- **Language mix:** ~92.8% Go Template, ~7.2% Makefile
- **Main maintenance pattern:** chart updates and feature additions, mostly upstream-style merged PR commits

## What this repository contains

At the top level, the repository contains multiple Helm charts plus repo-level governance/config files:

- `.github`
- `CODEOWNERS`
- `README.md`
- `spacelift-flows-agent`
- `spacelift-flows`
- `spacelift-promex`
- `spacelift-self-hosted`
- `spacelift-worker-pool`
- `spacelift-workerpool-controller`
- `vcs-agent`

### Main charts and what they do

1. **`spacelift-worker-pool`**
   - Deploys a **Spacelift Worker Pool** on Kubernetes.
   - Uses a launcher plus Docker-in-Docker sidecar.
   - Important note: the chart is **deprecated** in favor of `spacelift-workerpool-controller`.

2. **`spacelift-workerpool-controller`**
   - Deploys the **Spacelift workerpool controller** for Kubernetes-native workers.
   - Includes support for **CRD generation**, **PDB settings**, and chart version regeneration through `make codegen-helm`.
   - This looks like one of the more actively maintained charts.

3. **`vcs-agent`**
   - Deploys a **Spacelift VCS Agent** as a Deployment.
   - Supports credentials via chart-managed secret or an **externally managed existing secret**.

4. **`spacelift-self-hosted`**
   - Deploys **self-hosted Spacelift**.
   - README is minimal and points users to external install guides.

5. **`spacelift-flows`**
   - Deploys **Spacelift Flows**, a workflow automation platform.
   - Expects application configuration via an external Kubernetes secret.
   - Separates server, worker, and gateway configuration.

6. **`spacelift-flows-agent`**
   - Deploys a **Flows Agent pool** to Kubernetes.
   - Includes RBAC/service account options and endpoint configuration.

7. **`spacelift-promex`**
   - Deploys a **Spacelift Prometheus exporter**.
   - Supports custom CA certificate injection.

## Repository structure quality

This repo is structured like a **multi-chart Helm monorepo**. That is a good fit for related deployment artifacts because:

- each chart has its own directory and README,
- ownership is segmented by chart,
- chart-specific docs exist close to the code,
- some operational automation exists for CRDs/versioning.

## Ownership and team boundaries

The `CODEOWNERS` file is well organized and suggests the repo is maintained by multiple teams with clear domain ownership:

- default: `@spacelift-io/platform`
- `vcs-agent`: `@spacelift-io/ecosystem-backend`
- `spacelift-self-hosted`: `@spacelift-io/enterprise-ready-backend`
- `spacelift-flows`, `spacelift-flows-agent`: `@spacelift-io/flows`
- `spacelift-worker-pool`, `spacelift-workerpool-controller`: `@spacelift-io/foundations`
- `spacelift-promex`: `@spacelift-io/platform`

That usually indicates a **mature internal ownership model**, even though this fork itself shows no stars/issues activity.

## Development workflow insights

The root README says releases are expected to use tags in the format `v<version>`, and publishing is supposed to happen automatically for chart releases. However, in this fork I found **no releases** and no tags surfaced from the tool output.

There is also evidence of local codegen workflow for at least one chart:

- `spacelift-workerpool-controller/Makefile`
- target: `make codegen-helm`
- regenerates CRD templates
- updates `Chart.yaml` version markers
- injects optional Helm annotations like keep-on-uninstall behavior into generated CRDs

That tells me this repo is not just static YAML; at least part of it has a **build/generation step**.

## Recent development activity

Recent commits suggest the repository is **actively evolving**, especially around operational robustness and enterprise deployment flexibility. The latest visible commits include:

- **2026-06-18** — add conditional MQTT service support
- **2026-06-17** — add existing secret support for `vcs-agent`
- **2026-06-17** — update `k8s-workerpool-controller` to controller version `v0.0.35`
- **2026-06-11** — add PDB support for `spacelift-self-hosted`
- **2026-06-11** — add `terminationGracePeriodSeconds` to `spacelift-self-hosted`
- **2026-06-09** — add `extraManifests` support to `spacelift-self-hosted`
- **2026-05-26** — add service account to flows
- **2026-05-13** — fix workerpool-controller PDB/template guard behavior

### What that trend suggests

The active themes are:

- **secret management improvements**
- **self-hosted deployment hardening**
- **Kubernetes lifecycle safety** like PDBs
- **controller-based worker pool evolution**
- **extensibility knobs** for enterprise users

That usually means the charts are being used in real environments where operators need safer upgrades and more flexible secret/config handling.

## Strengths

- Clear modular chart layout
- Team ownership is explicitly defined
- Active maintenance signals from recent commits
- Good operational features in docs:
  - external secret support
  - PDB controls
  - custom CA certs
  - RBAC/service account settings
- Includes both legacy and newer deployment models, with deprecation guidance

## Weaknesses / risks

- Root README appears **outdated/incomplete** relative to actual repo contents:
  - It mentions mainly the worker pool and the VCS agent, but the repo now contains several more charts
- `spacelift-self-hosted` README is very thin
- The repo is a **fork**, so changes may lag, diverge from, or duplicate upstream
- No releases/tags visible in this fork output, which may reduce confidence if you intend to consume this fork directly
- `spacelift-worker-pool` is deprecated but still present, so users may choose the older path unless guided carefully

## Maturity assessment

My assessment: **moderately mature chart repository with active upstream-style maintenance, but this specific fork looks more like a mirror/working fork than a fully independent product distribution repo.**

Why:
- strong code ownership patterns,
- multiple specialized charts,
- recent operational improvements,
- but low independent repo signals in this fork itself: no releases, no stars, no issues.

## Best use case for this repo

This repo is best suited if you want to:

- study or customize **Spacelift Helm charts**,
- deploy Spacelift components into Kubernetes,
- compare chart implementations across components,
- fork and adapt charts for internal platform engineering needs.

## If you are evaluating whether to use it

Use it if:
- you need chart source for Spacelift components,
- you want to customize Helm templates,
- you are comfortable following upstream Spacelift chart evolution.

Be cautious if:
- you need a stable, independently versioned distribution from this fork,
- you expect polished release artifacts directly in this repository,
- you need comprehensive root-level documentation.

## Most important files/directories to inspect first

Start with these:

- `README.md`
- `CODEOWNERS`
- `spacelift-workerpool-controller/README.md`
- `spacelift-worker-pool/README.md`
- `vcs-agent/README.md`
- `spacelift-flows/README.md`
- `spacelift-flows-agent/README.md`
- `spacelift-promex/README.md`
- `spacelift-self-hosted/README.md`

## Bottom-line verdict

**This is a Helm chart monorepo for deploying Spacelift ecosystem components on Kubernetes, with the strongest signs of ongoing investment around workerpool-controller, self-hosted deployment flexibility, and secret-management improvements.** As a fork, it looks technically useful and actively synced, but not yet polished as a standalone distribution channel.

If you want, I can next give you one of these:
1. a **chart-by-chart deep dive**,
2. an **architecture map of the repo**,
3. a **maintainer-focused review with improvement suggestions**,
4. or a **production-readiness assessment**.
