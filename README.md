# flux

## prereq

- Install [Docker](https://docs.docker.com/desktop/) or [Podman](https://podman.io/docs/installation)

> if using `Podman` change the following settings
> - "dev.containers.dockerPath": "podman"
> - "dev.containers.dockerComposePath": "podman-compose"

- Install Dev Container inside VSCode `ms-vscode-remote.remote-containers`
- GitHub Account

## Bootstrap repo

```
gh auth login
```

```
gh repo create flux-config --public
```

```
export GITHUB_TOKEN=$(gh auth token)
```

```
flux bootstrap github \
  --owner=jimpaine \
  --repository=flux-config \
  --path=clusters/demo \
  --personal
```

## setup Azure environment

```
az login --use-device-code
```

```
az account set -s <subscription-id>
```

```
export RESOURCE_GROUP_NAME=flux-demo-$(echo $RANDOM)
az group create --name ${RESOURCE_GROUP_NAME} --location uksouth

az deployment group create -g ${RESOURCE_GROUP_NAME} --template-uri https://github.com/Azure/AKS-Construction/releases/download/0.9.15/main.json --parameters \
	resourceName=az-k8s-$(echo $RANDOM) \
	registries_sku=Basic \
	acrPushRolePrincipalId=$(az ad signed-in-user show --query id --out tsv) \
	fileCSIDriver=false \
	diskCSIDriver=false \
	fluxGitOpsAddon=true
```