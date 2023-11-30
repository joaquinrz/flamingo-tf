# Two GitOps Titans, One Powerful Solution: Flamingo (Flux + Argo)!

![License](https://img.shields.io/badge/license-MIT-green.svg)

## Overview

The landscape of Kubernetes and containerized applications has evolved rapidly in recent years, demanding innovative solutions to manage and orchestrate deployments seamlessly. Flamingo, the Flux subsystem for ArgoCD, represents a powerful integration of these two GitOps titans, supercharging your workflows. This dynamic duo creates a seamless and effective GitOps-driven platform for managing Kubernetes applications and infrastructure. This repository will guide you step-by-step on how to get started with Flamingo and provide an example of how we can integrate Flamingo with the Weave GitOps Terraform Controller. Hope you enjoy this content!


## Step 1: Install Prerequisites

To get started with the demo, we will need some prerequisites. We will be installing via [Homebrew](https://brew.sh/)


```bash
# Install Kubernetes in Docker (kind) CLI (you can also use k3d or any other kubernetes cluster of your choice)
brew install kind

# Install Flux CLI 
brew install fluxcd/tap/flux

# install Flamingo CLI 
# with Homebrew
brew install flux-subsystem-argo/tap/flamingo

```

## Step 2: Create a kind cluster and bootstrap it with our tools


```bash
# Create a kind Cluster
kind create cluster --name flamingo-demo

# Install Flux Controllers (note: the ideal way to install flux using true GitOps will be using 'flux bootstrap', but in this case we are using 'flux install' to simplify the process) 
flux install

# Install Flamingo
flamingo install

# Verify our pods are running correctly
kubectl get pods -A

```

## Step 3: Access Flamingo UI

``` bash
# Like a normal Argo CD instance, please firstly obtain the initial password by running the following command to login. The default username is admin.
flamingo show-init-password

# Portforward to http://localhost:8080
kubectl -n argocd port-forward svc/argocd-server 8080:443

```

## Step 4: Install Terraform Controller

The Weave GitOps Terraform Controller is a reliable controller for Flux to reconcile Terraform resources in the GitOps way. On the next steps we will be installing the controller CRDs so that we can then deploy our terraform resources.

``` bash
# Add tf-controller helm repository
helm repo add tf-controller https://weaveworks.github.io/tf-controller/

# Install tf-controller
helm upgrade -i tf-controller tf-controller/tf-controller \
    --namespace flux-system

# Verify our pods are running correctly
kubectl get pods -n flux-system

```

## Step 5: Deploy our Terraform file

We will install a simple hello world Terraform file. This hello world Terraform is this Git repository and contains everything we need.

On the Flamingo UI, create an App to manage our Terraform resource with Flux Subsystem for Argo. Please use the following configuration for the new App (helloworld)

```
Application Name: helloworld
Project: default
Sync Policy: Manual
Sync Options:
☑️ Auto-Create Namespace
☑️ Apply Out Of Sync Only
☑️ Use Flux Subsystem
☑️ Auto-Create Flux Resources
Repository URL: https://github.com/joaquinrz/flamingo-tf
Revision: main
Path: ./deploy/infra
Cluster URL: https://kubernetes.default.svc
Namespace: dev
```

After create the App, press Sync button once and wait. You could also press Refresh to see if the graph already there. If everything is correct, you would get something like the following screenshot, with a nice Terraform icon!!

Note: this example was taken from [here](Note: Th https://flux-subsystem-argo.github.io/website/tutorials/terraform/). Please reference it for further details and examples.

## Step 6: Install some apps using the Flamingo CLI

```bash
kubectl apply -f deploy/podinfo/podinfo-ks.yaml

# Generate a Flamingo application to visualize the podinfo-ks objects.
flamingo generate-app \
  --app-name=podinfo-ks \
  -n podinfo-kustomize ks/podinfo

kubectl apply -f deploy/podinfo/podinfo-helm.yaml

# Generate a Flamingo application to visualize the podinfo-helm objects.
flamingo generate-app \
  --app-name=podinfo-hr \
  -n podinfo-helm hr/podinfo
```
