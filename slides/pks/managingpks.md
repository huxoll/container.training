# Pivotal Container Service (PKS) Overview

Before we get to the lab, let's look at the PKS archtecture and how its managed.

.center[![PKS Logo](images/pks.png)]

---

## Overview

Enterprise Pivotal Container Service (Enterprise PKS) enables operators to provision, operate, and manage enterprise-grade Kubernetes clusters using BOSH and Pivotal Ops Manager.
<br><br><br>
Enterprise PKS uses the On-Demand Broker to deploy Cloud Foundry Container Runtime, a BOSH release that offers a uniform way to instantiate, deploy, and manage highly available Kubernetes clusters on a cloud platform using BOSH.

---

## Features

Enterprise PKS has the following features:

- Kubernetes compatibility: Constant compatibility with current stable release of Kubernetes

- Production-ready: Highly available from applications to infrastructure, with no single points of failure

- BOSH advantages: Built-in health checks, scaling, auto-healing and rolling upgrades

- Fully automated operations: Fully automated deploy, scale, patch, and upgrade experience

- Multi-cloud: Consistent operational experience across multiple clouds

On vSphere, Enterprise PKS supports deploying and running Kubernetes clusters in air-gapped environments.

---

class:pic

## Conceptual Overview

![PKS Arch Overview](images/pks-arch.png)

---

## PKS Control Plane

The PKS control plane is deployed on a single VM that includes the following components:

- The PKS API server

- The PKS Broker

- A User Account and Authentication (UAA) server

---
class:pic

## PKS Control Plane

![PKS Arch](images/pks-overview.png)]

---

## UAA

When a user logs in to or logs out of the PKS API through the PKS CLI, the PKS CLI communicates with UAA to authenticate them. The PKS API permits only authenticated users to manage Kubernetes clusters.

UAA must be configured with the appropriate users and user permissions.

---

## PKS API

Through the PKS CLI, users instruct the PKS API server to deploy, scale up, and delete Kubernetes clusters as well as show cluster details and plans. The PKS API can also write Kubernetes cluster credentials to a local kubeconfig file, which enables users to connect to a cluster through kubectl.

The PKS API sends all cluster management requests, except read-only requests, to the PKS Broker.

---

## PKS Broker

When the PKS API receives a request to modify a Kubernetes cluster, it instructs the PKS Broker to make the requested change.

The PKS Broker consists of an On-Demand Service Broker and a Service Adapter. The PKS Broker generates a BOSH manifest and instructs the BOSH Director to deploy or delete the Kubernetes cluster.

For Enterprise PKS deployments on vSphere with NSX-T, there is an additional component, the Enterprise PKS NSX-T Proxy Broker. The PKS API communicates with the PKS NSX-T Proxy Broker, which in turn communicates with the NSX Manager to provision the Node Networking resources. The PKS NSX-T Proxy Broker then forwards the request to the On-Demand Service Broker to deploy the cluster

---

## PKS Cluster Management

Users interact with Enterprise PKS and Enterprise PKS-deployed Kubernetes clusters in two ways:

- Deploying Kubernetes clusters with BOSH and managing their lifecycle. These tasks are performed using the PKS Command Line Interface (PKS CLI) and the PKS control plane.

.center[(e.g. ```cli-vm:~$ pks create cluster...```)]
    
- Deploying and managing container-based workloads on Kubernetes clusters. These tasks are performed using the Kubernetes CLI, _kubectl_

.center[(e.g. ```cli-vm:~$ kubectl get pods```)]

---

## Cluster Lifecycle Management
The PKS control plane enables users to deploy and manage Kubernetes clusters.

For communicating with the PKS control plane, Enterprise PKS provides a command line interface, the PKS CLI.

.center[(e.g. ```cli-vm:~$ pks delete cluster...```)]

---

## PKS Integration with NSX-T

There are three supported topologies in which to deploy NSX-T with Enterprise Pivotal Container Service (Enterprise PKS)

- NAT

- No NAT with Virtual Switch

- No NAT with Logical Switch

---

## NAT Topology
- PKS control plane (Ops Manager, BOSH Director, and PKS VM) components are all located on a logical switch that has undergone Network Address Translation on a T0.

- Kubernetes cluster master and worker nodes are located on a logical switch that has undergone Network Address Translation on a T0. This requires DNAT rules to allow access to Kubernetes APIs.

.center[![NAT Topology](images/nsxt-topology-nat.png)]

---

## No-NAT with Virtual Switch Topology
- PKS control plane (Ops Manager, BOSH Director, and PKS VM) components are using corporate routable IP addresses.

- Kubernetes cluster master and worker nodes are using corporate routable IP addresses.

- The PKS control plane is deployed outside of the NSX-T network and the Kubernetes clusters are deployed and managed within the NSX-T network. Since BOSH needs routable access to the Kubernetes Nodes to monitor and manage them, the Kubernetes Nodes need routable access.
.center[![No-NAT VSS](images/nsxt-topology-no-nat-virtual-switch.png)]

---

## No-NAT with Logical Switch Topology
- PKS control plane (Ops Manager, BOSH Director, and PKS VM) components are using corporate routable IP addresses.

- Kubernetes cluster master and worker nodes are using corporate routable IP addresses.

- The PKS control plane is deployed inside of the NSX-T network. Both the PKS control plane components (VMs) and the Kubernetes Nodes use corporate routable IP addresses.
.center[![No-NAT Logical](images/nsxt-topology-no-nat-logical-switch.png)]
