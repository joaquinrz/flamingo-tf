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

# Ensure our pods are running correctly
kubectl get pods -A

```

## Step 3: Access Flamingo UI

``` bash
# Like a normal Argo CD instance, please firstly obtain the initial password by running the following command to login. The default username is admin.
flamingo show-init-password

# Portforward to http://localhost:8080
kubectl -n argocd port-forward svc/argocd-server 8080:443


```