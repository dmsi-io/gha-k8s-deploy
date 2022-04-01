# GHA Kubernetes Deploy

[![release][release-badge]][release]

The purpose of this GitHub Action is to handle the setup of GCloud and kubectl resources, docker building and publishing, as well as deployment of kubernetes objects.

## Setup

This action is reliant on a Service Account with the following permissions:

- Kubernetes Engine Admin
- Storage Admin

Additionally, it is recommended to use Workload Identity Federation. If this is not setup follow the steps here: https://github.com/google-github-actions/auth#setup

## Inputs

| NAME                    | DESCRIPTION                                                                                      | TYPE     | REQUIRED | DEFAULT           |
| ----------------------- | ------------------------------------------------------------------------------------------------ | -------- | -------- | ----------------- |
| `GCP_IDENTITY_PROVIDER` | GCP Workload Identity Provider.                                                                  | `string` | `true`\* |                   |
| `GCP_SERVICE_ACCOUNT`   | GCP Service Account email.                                                                       | `string` | `true`\* |                   |
| `GCP_SA_KEY`            | GCP Service Account Key (JSON).                                                                  | `string` | `true`\* |                   |
| `GKE_CLUSTER_NAME`      | Google Kubernetes Engine Cluster name.                                                           | `string` | `true`   |                   |
| `GCP_ZONE`              | GCP Zone.                                                                                        | `string` | `true`   |                   |
| `GCP_PROJECT_ID`        | GCP Project ID.                                                                                  | `string` | `true`   |                   |
| `print_gcloud_info`     | Flag to optionally print gcloud info after authenticating.                                       | `bool`   | `false`  | `false`           |
| `print_environment`     | Flag to optionally print environment variables.                                                  | `bool`   | `false`  | `false`           |
| `k8s_directory`         | Location of k8s config files.                                                                    | `string` | `false`  | `k8s`             |
| `namespace`             | Filename of namespace config.                                                                    | `string` | `false`  | `namespace.yaml`  |
| `secret`                | Name of secret to copy from default namespace.                                                   | `string` | `false`  |                   |
| `configmap`             | Filename of configmap config. (Can also be used for any other arbitrary K8S artifact to deploy). | `string` | `false`  | `configmap.yaml`  |
| `deployment`            | Filename of deployment config.                                                                   | `string` | `false`  | `deployment.yaml` |
| `service`               | Filename of service config.                                                                      | `string` | `false`  | `service.yaml`    |
| `ingress`               | Filename of ingress config.                                                                      | `string` | `false`  | `ingress.yaml`    |
| `skip_docker`           | Flag to skip docker build and push steps.                                                        | `bool`   | `false`  | `false`           |
| `docker_directory`      | Directory of Dockerfile.                                                                         | `string` | `false`  | `./`              |
| `skip_k8s_deploy`       | Flag to skip Kubernetes artifact deployment steps.                                               | `bool`   | `false`  | `false`           |
| `skip_deploy_status`    | Flag to skip deployment status check.                                                            | `bool`   | `false`  | `false`           |

> It is recommended to use Workload Identity Federation with the `GCP_IDENTITY_PROVIDER` and `GCP_SERVICE_ACCOUNT` inputs. `GCP_SA_KEY` will still work with `v1` tags.

## Outputs

| NAME  | DESCRIPTION                                                                                                                                 | TYPE     |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `url` | The namespace URL generated by the deployment process. Will be an empty string if k8s deploy is skipped or no namespace config is provided. | `string` |

## Example

```yaml
name: Kubernetes Deployment
on:
  push:
    branches:
      - develop
      - 'feature/*'

jobs:
  build-deploy:
    name: Build and Deploy Kubernetes
    runs-on: ubuntu-latest

    permissions:
      contents: 'read'
      id-token: 'write'

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Export Environment Variables
        uses: dmsi-io/gha-env-variables@main
        with:
          TLD: ${{ secrets.TOP_LEVEL_DOMAIN }}
          GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}

      - name: Deploy Kubernetes
        uses: dmsi-io/gha-k8s-deploy@main
        with:
          GCP_IDENTITY_PROVIDER: ${{ secrets.GCP_IDENTITY_PROVIDER }}
          GCP_SERVICE_ACCOUNT: ${{ secrets.GCP_SERVICE_ACCOUNT }}
          GKE_CLUSTER_NAME: ${{ secrets.GCP_CLUSTER_NAME }}
          GCP_ZONE: ${{ secrets.GCP_ZONE }}
          GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
```

> Workload Identity Federation requires access to the id-token permission and thus the outlined permissions in the example above are required.

#### With Service Account Credentials JSON

```yaml
name: Kubernetes Deployment
on:
  push:
    branches:
      - develop
      - 'feature/*'

jobs:
  build-deploy:
    name: Build and Deploy Kubernetes
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Export Environment Variables
        uses: dmsi-io/gha-env-variables@main
        with:
          TLD: ${{ secrets.TOP_LEVEL_DOMAIN }}
          GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}

      - name: Deploy Kubernetes
        uses: dmsi-io/gha-k8s-deploy@main
        with:
          GCP_SA_KEY: ${{ secrets.GCP_SA_KEY }}
          GKE_CLUSTER_NAME: ${{ secrets.GCP_CLUSTER_NAME }}
          GCP_ZONE: ${{ secrets.GCP_ZONE }}
          GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
```

<!-- badge links -->

[release]: https://github.com/dmsi-io/gha-k8s-deploy/releases
[release-badge]: https://img.shields.io/github/v/release/dmsi-io/gha-k8s-deploy?style=for-the-badge&logo=github
