# Typhoon <img align="right" src="https://storage.googleapis.com/dghubble/spin.png">

Typhoon is a minimal and free Kubernetes distribution.

* Minimal, stable base Kubernetes distribution
* Declarative infrastructure and configuration
* [Free](#social-contract) (freedom and cost) and privacy-respecting
* Practical for labs, datacenters, and clouds

Typhoon distributes upstream Kubernetes, architectural conventions, and cluster addons, much like a GNU/Linux distribution provides the Linux kernel and userspace components.

## Features

* Kubernetes v1.7.5 (upstream, via [kubernetes-incubator/bootkube](https://github.com/kubernetes-incubator/bootkube))
* Single or multi-master, workloads isolated on workers, [Calico](https://www.projectcalico.org/) or [flannel](https://github.com/coreos/flannel) networking
* On-cluster etcd with TLS, [RBAC](https://kubernetes.io/docs/admin/authorization/rbac/)-enabled, [network policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
* Ready for Ingress, Dashboards, Metrics and other optional [addons](addons/overview.md)
* Provided via Terraform Modules

## Modules

Typhoon provides a Terraform Module for each supported operating system and platform.

| Platform      | Operating System | Terraform Module | Status |
|---------------|------------------|------------------|--------|
| AWS           | Container Linux  | [aws/container-linux/kubernetes](aws.md) | alpha |
| Bare-Metal    | Container Linux  | [bare-metal/container-linux/kubernetes](bare-metal.md) | production |
| Digital Ocean | Container Linux  | [digital-ocean/container-linux/kubernetes](digital-ocean.md) | beta |
| Google Cloud  | Container Linux  | [google-cloud/container-linux/kubernetes](google-cloud.md) | production |

## Usage

* [Concepts](concepts.md)
* Tutorials
    * [AWS](aws.md)
    * [Bare-Metal](bare-metal.md)
    * [Digital Ocean](digital-ocean.md)
    * [Google-Cloud](google-cloud.md)

## Example

Define a Kubernetes cluster by using the Terraform module for your chosen platform and operating system. Here's a minimal example.

```tf
module "google-cloud-yavin" {
  source = "git::https://github.com/poseidon/typhoon//google-cloud/container-linux/kubernetes"

  # Google Cloud
  zone          = "us-central1-c"
  dns_zone      = "example.com"
  dns_zone_name = "example-zone"
  os_image      = "coreos-stable-1465-6-0-v20170817"

  cluster_name       = "yavin"
  controller_count   = 1
  worker_count       = 2
  ssh_authorized_key = "ssh-rsa AAAAB3Nz..."

  # output assets dir
  asset_dir = "/home/user/.secrets/clusters/yavin"
}
```

Fetch modules, plan the changes to be made, and apply the changes.

```sh
$ terraform get --update
$ terraform plan
Plan: 64 to add, 0 to change, 0 to destroy.
$ terraform apply
Apply complete! Resources: 64 added, 0 changed, 0 destroyed.
```

In 5-10 minutes (varies by platform), the cluster will be ready. This Google Cloud example creates a `yavin.example.com` DNS record to resolve to a network load balancer across controller nodes.

```
$ KUBECONFIG=/home/user/.secrets/clusters/yavin/auth/kubeconfig
$ kubectl get nodes
NAME                                          STATUS   AGE    VERSION
yavin-controller-1682.c.example-com.internal  Ready    6m     v1.7.5+coreos.0
yavin-worker-jrbf.c.example-com.internal      Ready    5m     v1.7.5+coreos.0
yavin-worker-mzdm.c.example-com.internal      Ready    5m     v1.7.5+coreos.0
```

List the pods.

```
$ kubectl get pods --all-namespaces
NAMESPACE     NAME                                      READY  STATUS    RESTARTS  AGE
kube-system   calico-node-1cs8z                         2/2    Running   0         6m
kube-system   calico-node-d1l5b                         2/2    Running   0         6m
kube-system   calico-node-sp9ps                         2/2    Running   0         6m
kube-system   etcd-operator-3329263108-f443m            1/1    Running   1         6m
kube-system   kube-apiserver-zppls                      1/1    Running   0         6m
kube-system   kube-controller-manager-3271970485-gh9kt  1/1    Running   0         6m
kube-system   kube-controller-manager-3271970485-h90v8  1/1    Running   1         6m
kube-system   kube-dns-1187388186-zj5dl                 3/3    Running   0         6m
kube-system   kube-etcd-0000                            1/1    Running   0         5m
kube-system   kube-etcd-network-checkpointer-crznb      1/1    Running   0         6m
kube-system   kube-proxy-117v6                          1/1    Running   0         6m
kube-system   kube-proxy-9886n                          1/1    Running   0         6m
kube-system   kube-proxy-njn47                          1/1    Running   0         6m
kube-system   kube-scheduler-3895335239-5x87r           1/1    Running   0         6m
kube-system   kube-scheduler-3895335239-bzrrt           1/1    Running   1         6m
kube-system   pod-checkpointer-l6lrt                    1/1    Running   0         6m
```

## Help

Ask questions on the IRC #typhoon channel on [freenode.net](http://freenode.net/).

## Background

Typhoon powers the author's cloud and colocation clusters. The project has evolved through operational experience and Kubernetes changes. Typhoon is shared under a free license to allow others to use the work freely and contribute to its upkeep.

Typhoon addresses real world needs, which you may share. It is honest about limitations or areas that aren't mature yet. It avoids buzzword bingo and hype. It does not aim to be the one-solution-fits-all distro. An ecosystem of free (or enterprise) Kubernetes distros is healthy.

## Social Contract

Typhoon is not a product, trial, or free-tier. It is not run by a company, does not offer support or services, and does not accept or make any money. It is not associated with any operating system or platform vendor.

Typhoon clusters will contain only [free](https://www.debian.org/intro/free) components. Cluster components will not collect data on users without their permission.

*Disclosure: The author works for CoreOS and previously wrote Matchbox and original Tectonic for bare-metal and AWS. This project is not associated with CoreOS.*
