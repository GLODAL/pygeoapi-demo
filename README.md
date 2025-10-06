# Introduction

This repository provides an introductory hands-on tutorial for setting up an OGC API Server using **pygeoapi**.

# kubectl

For an overview of [Kubernetes](https://kubernetes.io/docs/concepts/overview/), the popular cluster management platform, see the official documentation.
In this tutorial, we will deploy an OGC API Server on a Kubernetes cluster.

`kubectl` is the command-line tool used to manage resources and services in a Kubernetes cluster.
Open a terminal and run:

```bash
kubectl get nodes
```

This command lists all nodes in the cluster.

To view detailed information about a specific node, use:

```bash
kubectl describe node <NODE_NAME>
```

For example:

```bash
kubectl describe node i5-12400
```

Each user on this cluster can control Kubernetes resources and services within their **namespace**, which corresponds to their GitHub account name in lowercase (e.g., `abc` for the user `AbC`).

To list running [pods](https://kubernetes.io/docs/concepts/workloads/pods/) (the smallest compute units in Kubernetes) in your namespace, run:

```bash
kubectl -n <NAMESPACE> get pods
```

If you see the message
`No resources found in <namespace> namespace`,
it means no services have been deployed yet.

# Configure pygeoapi

Next, prepare the configuration for pygeoapi.
Create a folder for your configuration files under `~/pygeoapi-nfs` using your namespace as the folder name:

```bash
mkdir -p ~/pygeoapi-nfs/<YOUR_NAMESPACE>
cp pygeoapi-config.yml ~/pygeoapi-nfs/<YOUR_NAMESPACE>/
```

For detailed information on configuring pygeoapi, especially for data publication, see the [pygeoapi documentation](https://docs.pygeoapi.io/en/latest/data-publishing/index.html).

# Deploy the pygeoapi service

To deploy the service, you need to configure a **deployment manifest** that defines the compute resources and network settings.

```bash
cp pygeoapi.template.yaml pygeoapi.yaml
# Edit pygeoapi.yaml for your <YOUR_NAMESPACE>
```

After editing, deploy pygeoapi using:

```bash
kubectl -n <YOUR_NAMESPACE> apply -f pygeoapi.yaml
```

Once deployed, your service will be accessible at:

```
https://<YOUR_NAMESPACE>-pygeoapi.glodal-inc.net
```

You can check the running backend pods with:

```bash
kubectl -n <YOUR_NAMESPACE> get pods
```

If the pods show the status `Running`, the service is likely healthy—but not always. Internal issues may still occur due to misconfigurations.
For more detailed diagnostics:

```bash
kubectl -n <YOUR_NAMESPACE> describe pod <POD_NAME>
kubectl -n <YOUR_NAMESPACE> logs <POD_NAME>
```

When you modify configuration files (e.g., `local.config.yml`), apply the changes to the running service using:

```bash
kubectl rollout restart deployment pygeoapi -n <YOUR_NAMESPACE>
```

# Next Steps

## Import datasets of interest

Place your datasets of interest under:

```
~/pygeoapi-nfs/pygeoapi/tests/data/
```

Then configure the dataset in your `local.config.yml`.
See the [pygeoapi documentation on data publication](https://docs.pygeoapi.io/en/latest/data-publishing/index.html) for details.

## Explore operational setups on AWS

This demo environment is intended for learning, not for production use.
For operational purposes, consider deploying pygeoapi on AWS using one of the following approaches:

1. **Docker on an EC2 instance** – Quick and easy, but not scalable. Performance may degrade with many simultaneous requests.
2. **Amazon Elastic Kubernetes Service (EKS)** – A managed Kubernetes service on AWS offering flexible and scalable resource management.

## Explore additional OGC APIs

In this tutorial, we focused on the **Features API**.
However, pygeoapi also supports other OGC APIs, including:

* Coverages
* Maps
* Tiles
* Processes
* Records
* Environmental Data Retrieval (EDR)

You are encouraged to explore these APIs for more advanced applications and solutions.
