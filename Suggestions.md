## Date: 2026-06-22

Top improvements I’d suggest for `nvm-my/spacelift-helm-charts` are: **fix the top-level docs, standardize chart quality across all charts, add stronger CI validation, and make security/versioning defaults more production-friendly.**

## Highest-impact suggestions

### 1. Update the root README to match the actual repo
Right now the root `README.md` only calls out worker pool and VCS agent docs, but the repo currently contains at least seven charts: `spacelift-worker-pool`, `spacelift-workerpool-controller`, `vcs-agent`, `spacelift-self-hosted`, `spacelift-flows`, `spacelift-flows-agent`, and `spacelift-promex`.

**Why improve it**
- New users won’t discover most of the repo from the landing page.
- Deprecated vs preferred paths are easy to miss.
- The repo feels less maintained than it actually is.

**Suggestion**
- Add a chart index table with:
  - chart name
  - purpose
  - status (`active`, `deprecated`)
  - install command
  - link to per-chart README
- Explicitly mark `spacelift-worker-pool` as deprecated and point users to `spacelift-workerpool-controller`.

---

### 2. Bring documentation quality up to the level of the better charts
Docs are uneven today:
- `spacelift-flows`, `spacelift-flows-agent`, `vcs-agent`, and `spacelift-worker-pool` have useful usage/config docs.
- `spacelift-self-hosted` and `spacelift-workerpool-controller` are much lighter.
- `spacelift-self-hosted/README.md` is especially minimal despite having many tunables in `values.yaml` like `server`, `drain`, `scheduler`, `vcsGateway`, `mqttService`, and `extraManifests`.

**Suggestion**
For each chart README, standardize sections:
- What it deploys
- Prerequisites
- Quick start
- Required secrets
- Key values
- Example production values
- Upgrade notes
- Troubleshooting
- Uninstall behavior

**Biggest doc gap to prioritize first**
- `spacelift-self-hosted`
- then `spacelift-workerpool-controller`

---

### 3. Add schema validation to all charts
I only saw `values.schema.json` in `spacelift-self-hosted`. That’s good, but the rest of the charts appear to rely only on comments in `values.yaml`.

**Why improve it**
- Helm catches bad user input earlier.
- Better editor autocomplete and UX.
- Fewer support issues from invalid values.

**Suggestion**
Add `values.schema.json` for:
- `vcs-agent`
- `spacelift-worker-pool`
- `spacelift-workerpool-controller`
- `spacelift-flows`
- `spacelift-flows-agent`
- `spacelift-promex`

Start with required fields like:
- secrets / token settings
- endpoint URLs
- booleans for feature flags
- enum-like values where applicable

---

### 4. Expand CI beyond lint/package
Current workflows do useful things:
- `build-chart.yml` lints and packages charts
- `check-workerpool-chart-codegen.yml` verifies `make codegen-helm` for `spacelift-workerpool-controller`

But CI still looks a bit narrow.

**Suggestion**
Add:
- `helm template` smoke tests for each chart with representative values files
- matrix tests for key variants:
  - secret creation vs existing secret
  - ingress enabled/disabled
  - statefulset vs deployment for `spacelift-worker-pool`
  - namespace-scoped vs cluster-wide `spacelift-workerpool-controller`
- Kubernetes manifest validation with `kubeconform` or `kubeval`
- chart-testing (`ct lint`, `ct install`) if practical

This would catch template regressions that plain `helm lint` misses.

---

### 5. Pin GitHub Actions to immutable versions
Your workflows use `actions/checkout@main`, which is risky.

**Suggestion**
Replace floating refs like:
- `actions/checkout@main`

with:
- a tagged release, or ideally
- a full commit SHA pin

This is a small change with a good security payoff.

---

### 6. Improve versioning consistency across charts
Versioning is mixed:
- `spacelift-workerpool-controller/Chart.yaml` uses versions like `v0.17.0`
- some other charts use plain semver like `0.1.0`
- several images default to `latest` or `agent-latest`

**Suggestion**
Standardize:
- chart versions: plain semver if possible
- app versions: explicit image/app release version
- avoid `latest` defaults where safe

I understand some charts intentionally use `latest` for rollout behavior, but that should be:
- clearly justified in docs
- called out as a tradeoff
- optionally overridable with a pinned tag example for controlled environments

---

### 7. Add example values files per chart
Some READMEs have inline snippets, but example files would be more reusable.

**Suggestion**
Create `examples/` directories such as:
- `spacelift-self-hosted/examples/minimal.yaml`
- `spacelift-self-hosted/examples/aws-alb.yaml`
- `spacelift-self-hosted/examples/gke-gateway.yaml`
- `vcs-agent/examples/existing-secret.yaml`
- `spacelift-workerpool-controller/examples/namespaced.yaml`
- `spacelift-flows/examples/basic.yaml`

This also makes CI easier because examples can be templated in tests.

---

### 8. Improve deprecation guidance for `spacelift-worker-pool`
The deprecation notice is present in `spacelift-worker-pool/README.md`, which is good.

**Suggestion**
Also add deprecation cues in:
- root README
- `Chart.yaml` description if appropriate
- release notes / changelog
- maybe a NOTES message on install/upgrade warning users to migrate

That reduces the chance that users keep adopting the old DinD-based chart by accident.

---

### 9. Add changelog / release notes discipline
The root README says releases are tag-driven, but the repo would be easier to trust with clearer human-readable release notes.

**Suggestion**
Add either:
- a root `CHANGELOG.md`, or
- per-release notes policy in GitHub Releases

Especially useful because this repo has multiple charts and users need to know which chart changed and whether it was breaking.

---

### 10. Tighten security defaults and document them better
There are good signs already:
- `spacelift-workerpool-controller` has hardened container security context
- `vcs-agent` supports existing secrets
- `spacelift-self-hosted` exposes service account and PDB controls

But security posture is inconsistent across charts.

**Suggestion**
Review each chart for:
- `runAsNonRoot`
- `readOnlyRootFilesystem`
- dropped Linux capabilities
- seccomp profile
- explicit service account behavior
- minimal RBAC
- image digest support where useful

Even if some workloads can’t fully adopt strict defaults, documenting the recommended hardened settings would help operators.

---

## Suggested priority order

If you want the best ROI, I’d do this in order:

1. **Fix root README**
2. **Expand `spacelift-self-hosted` docs**
3. **Add schemas for all charts**
4. **Strengthen CI with template/render tests**
5. **Pin GitHub Actions versions**
6. **Standardize versioning/examples/security docs**

---

## Concrete repo-specific observations behind these suggestions

These recommendations come from the current repo structure and files:
- root README is incomplete relative to actual charts
- `spacelift-self-hosted` has many templates and values, but minimal README
- `spacelift-workerpool-controller` has a local CRD generation flow via `make codegen-helm`
- CI currently focuses on lint/package and one codegen check
- only `spacelift-self-hosted` appears to ship a `values.schema.json`
- several charts expose advanced config knobs that would benefit from examples and validation

If you want, I can next turn this into:
1. a **prioritized improvement checklist**,  
2. a **draft README rewrite**, or  
3. a **GitHub issue list** for the repo.
