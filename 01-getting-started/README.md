# Getting Started with Helm

Install Helm on your machine and get familiar with the basic commands using the
official [quickstart guide](https://docs.helm.sh/using_helm/#quickstart)

## Prerequisites

- A Kubernetes cluster with kubectl configured to access it. You can use the [local machine solution](https://kubernetes.io/docs/setup/pick-right-solution/#local-machine-solutions) of your choice, or a cloud provider's hosted option. We'll use [AKS](https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough) but this tutorial will work for any Kubernetes option.

## Installing the Helm Client

If you already have Helm installed, check to see if it is [latest version](https://github.com/helm/helm/releases/latest), then upgrade if need be.

```console
$ helm version
```

On Mac, the easiest way to install or upgrade Helm is to use Homebrew.

If you need to install helm:

```console
$ brew install kubernetes-helm
```

If you need to upgrade helm:

```console
$ brew upgrade kubernetes-helm
```

On Ubuntu:

```console
$ sudo snap install helm --classic
```

And on Windows:

```console
$ choco install kubernetes-helm
```

> More installation methods are documented in the [documentation for installing
> Helm](https://docs.helm.sh/using_helm/#installing-helm)

## Initialize Helm and Install Tiller

Once you have Helm ready, you can initialize the local environment and install
Tiller into your Kubernetes cluster in one step.

```console
$ helm init
```

Helm will use whatever Kubernetes cluster your context is pointing to. Use
`kubectl` to learn about your contexts:

```console
$ kubectl config current-context
docker-for-desktop
```

You can verify your setup by running the version command.

```console
$ helm version
Client: &version.Version{SemVer:"v2.14.0", GitCommit:"05811b84a3f93603dd6c2fcfe57944dfa7ab7fd0", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.14.0", GitCommit:"05811b84a3f93603dd6c2fcfe57944dfa7ab7fd0", GitTreeState:"clean"}
```

Inspect the deployment if you get an error.

```console
$ kubectl -n kube-system describe deployment tiller-deploy
```

Once Tiller is up and running, you'll need to grant an admin role to the default
service account.

```console
$ kubectl create clusterrolebinding add-on-cluster-admin \
  --clusterrole=cluster-admin --serviceaccount=kube-system:default
```

IMPORTANT: Please note that this is ok for personal development, but for
production use cases, defining the exact roles and permissions on your cluster
requires a deeper knowledge of Kubernetes' RBAC model


Up next: [Installing Charts](../02-installing-charts/).
