# KubeIP Helm chart

This repository is the canonical source and public Helm repository for KubeIP charts.
Agent source and releases are maintained in [doitintl/kubeip](https://github.com/doitintl/kubeip).

## Install

```sh
helm repo add kubeip https://doitintl.github.io/kubeip-helm-chart/
helm repo update
helm upgrade --install kubeip kubeip/kubeip \
  --version 0.2.0 --namespace kube-system --values my-values.yaml
```

Chart 0.2.0 defaults to agent 2.3.0. Set `image.repository` without a tag and
`image.tag` to select another agent version. Configure `cloudProvider`, provider
credentials, and `daemonSet.nodeSelector` for your cluster before installation.
See the [agent setup guide](https://github.com/doitintl/kubeip#readme) for provider permissions.

When using taints, set `daemonSet.env.TAINT_KEY` and
`rbac.allowNodesPatchPermission=true`. `daemonSet.env.WAIT_FOR_NODE_ADDRESS` defaults
to true. Set it to false only when workloads can start after cloud assignment
without waiting for Node status to advertise the IP.

## Publishing

This follows the DoiT EKS Lens pattern: GitHub Releases host chart archives,
GitHub Pages serves the index, and `helm/chart-releaser-action` publishes using
`GITHUB_TOKEN`. No Docker Hub or GHCR credentials are required.

1. Edit `charts/kubeip`, bump its chart version, and update `appVersion` and the
   default image tag when changing the agent version.
2. Merge the validated change into `main`.
3. The workflow packages the chart, creates a `kubeip-<chart-version>` release,
   updates the `gh-pages` index, and deploys it to GitHub Pages.
4. Verify `helm pull kubeip/kubeip --version <chart-version>` after updating the repository.

Existing release versions are skipped on reruns. Always bump the chart version
when changing chart contents. The workflow can also be dispatched manually.
Artifact Hub registration is optional and separate from hosting the chart.
