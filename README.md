# gha-k8s-deploy

The purpose of this GitHub Action is to handle the setup of GCloud and kubectl resources, docker building and publishing, as well as deployment of kubernetes objects.

### Usage

```yaml
- name: Checkout
  uses: actions/checkout@v2

- name: Export Environment Variables
  uses: dmsi-io/gha-env-variables@v1
  with:
    TLD: ${{ secrets.TOP_LEVEL_DOMAIN }}
    GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}

- name: Deploy Kubernetes
  uses: dmsi-io/gha-k8s-deploy@v1
  with:
    GCP_SA_KEY: ${{ secrets.GCP_SA_KEY }}
    GKE_CLUSTER_NAME: ${{ secrets.GCP_STAGING_CLUSTER_NAME }}
    GCP_ZONE: ${{ secrets.GCP_ZONE }}
    GCP_PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
```

### Optional inputs

#### K8S Directory

Used to specify a different directory to check for k8s related config files. All k8s files to deploy my be under this directory.

> Example kubernetes configs can be found under the `k8s` directory. These are not used within this GitHub Action.

- Default: `k8s`

```yaml
with:
  k8s_directory: 'kubernetes'
```

#### Namespace

Used to specify a different file to deploy a Namespace object from. If the file does not exist, it will not try to deploy.

- Default: `namespace.yaml`

```yaml
with:
  namespace: 'name.yaml'
```

#### Secret

Used to specify a secret to copy from the default namespace into the namespace being created.

```yaml
with:
  secret: 'secret-env'
```

#### ConfigMap

Used to specify a different file to deploy a ConfigMap object from. If the file does not exist, it will not try to deploy.

- Default: `configmap.yaml`

```yaml
with:
  configmap: 'config.yaml'
```

#### Deployment

Used to specify a different file to deploy a Deployment object from. If the file does not exist, it will not try to deploy.

- Default: `deployment.yaml`

```yaml
with:
  deployment: 'deploy.yaml'
```

#### Service

Used to specify a different file to deploy a Service object from. If the file does not exist, it will not try to deploy.

- Default: `service.yaml`

```yaml
with:
  service: 'serv.yaml'
```

#### Ingress

Used to specify a different file to deploy a Ingress object from. If the file does not exist, it will not try to deploy.

- Default: `ingress.yaml`

```yaml
with:
  ingress: 'ing.yaml'
```

#### Skip Docker

Some repositories won't need to build and publish a Docker image, this flag allows the GHA to skip these steps.

- Default: `false`

```yaml
with:
  skip_docker: 'true'
```

#### Docker Directory

Sometimes the Dockerfile will not be located at the head of a repository, this input field allows the GHA to build the docker image from a Dockerfile located elsewhere.

- Default: `./`

```yaml
with:
  docker_directory: '.docker'
```

#### Skip Deployment Status

Sometimes when trying to debug a k8s deployment that refuses to deploy correctly, it can build up a lot of GHA minutes to wait for the timeout of deployment status check. Supplying this flag allows the GHA to skip checking if the deployment was successful.

> This step is also skipped if the `deployment` file is not supplied and thus not deployed.

- Default: `false`

```yaml
with:
  skip_deploy_status: 'true'
```

#### Print Environment Variables

Sometimes it is helpful to view the environment variables set to help debug. Supplying this flag will print `env | sort` to the console.

- Default: `false`

```yaml
with:
  print_environemnt: 'true'
```

#### Print GCloud Info

Sometimes it is helpful to view gcloud information to help debug. Supplying this flag will print out `gcloud info` after authenticating.

- Default: `false`

```yaml
with:
  print_gcloud_info: 'true'
```
