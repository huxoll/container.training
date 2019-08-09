# Setting up Kubernetes with PKS

- Before we can deploy our app, we need to:

    - Ensure everyone has credentials setup in PKS.
    - Install the PKS & kubectl CLI (we have an easy solution for this).
    - Log in to PKS.
    - Create a cluster.
    - Fetch the cluster credentials
    - Start using kubectl!

---

<!-- ##VERSION## -->
## Installing PKS & kubectl CLI

- For this workshop, we'll be controlling PKS & kubectl remotely.
- We'll need a client machine running the PKS & kubectl CLIs with access to the PKS environment.
- We've created a Dockerfile that will build an image to run a container with all the tools needed!

.exercise[

- Using ```git```, pull down the Dockerfile to your Docker host:
  ```bash
  git clone https://github.com/dahr/cli_container.git
  ```

- Build the image from the Dockerfile and run:
  ```bash
  cd cli_container
  docker build -t cli_client .
  docker run -it cli_client
  ```
]

---

## Authenticating API Requests

When a user with cluster access logs in to the PKS CLI, the CLI requests an access token for the user from the UAA server. If the request is successful, the UAA server returns an access token to the PKS CLI. When the user runs PKS CLI commands, for example, <code>pks clusters</code>, the CLI sends the request to the PKS API server and includes the user’s UAA token.

The PKS API sends a request to the UAA server to validate the user’s token. If the UAA server confirms that the token is valid, the PKS API uses the cluster information from the PKS broker to respond to the request. For example, if the user runs <code>pks clusters</code>, the CLI returns a list of the clusters that the user is authorized to manage.

---

## Creating our first K8s cluster

- PKS takes care of all the details of setting up a K8s cluster.
    - Installs Docker.
    - Installs Kubernetes packages.
    - Deploys the control plane.
    - Configures & joins the nodes.
    - Creates the network overlay (with NSX-T).
    - Configures high availability.

.exercise[
    *Note: Cluster creation can take up to 30 mins*<br><br>
    Using ```pks```, let's login & create our first cluster:
  ```bash
  pks login -a <your.pks.endpoint> -u <user> -p <password> -k
  pks create-cluster <cluster-name> --external-hostname <external-hostname.com> --plan small --num-nodes 3
  ```
]

---

## VM Sizing

When you configure plans in the Enterprise PKS tile, you provide VM sizes for the master and worker node VMs.

You select the number of master nodes when you configure the plan.

For worker node VMs, you select the number and size based on the needs of your workload. The sizing of master and worker node VMs is highly dependent on the characteristics of the workload. Adapt the recommendations in this topic based on your own workload requirements.

---

## Master Node VM Size

The master node VM size is linked to the number of worker nodes. If there are multiple master nodes, all master node VMs are the same size.

The VM sizing per master node can be found [here](https://docs.pivotal.io/pks/1-4/vm-sizing.html#node-sizing-custom).

To customize the size of the Kubernetes master node VM, see [Customize Master and Worker Node VM Size and Type](https://docs.pivotal.io/pks/1-4/vm-sizing.html#node-sizing-custom).

---

## Worker Node VM Number and Size

A maximum of 100 pods can run on a single worker node. The actual number of pods that each worker node runs depends on the workload type as well as the CPU and memory requirements of the workload.

To calculate the number and size of worker VMs you require, use the guidelines [here](https://docs.pivotal.io/pks/1-4/vm-sizing.html#node-sizing-custom).

---

## Customize Master and Worker Node VM Size and Type

You select the CPU, memory, and disk space for the Kubernetes node VMs from a set list in the Enterprise PKS tile. Master and worker node VM sizes and types are selected on a per-plan basis. For more information, see the Plans section of the Enterprise PKS installation topic for your IaaS. For example, [Installing Enterprise PKS on vSphere with NSX-T](https://docs.pivotal.io/pks/1-4/installing-nsx-t.html#plans).

While the list of available node VM types and sizes is extensive, the list may not provide the exact type and size of VM that you want. You can use the Ops Manager API to customize the size and types of the master and worker node VMs. For more information, see [How to Create or Remove Custom VM_TYPE Template using the Operations Manager API](https://community.pivotal.io/s/article/How-to-Create-or-Remove-Custom-VMTYPE-Template-using-the-Ops-Manager-API) in the Pivotal Knowledge Base.

---

## Checking the status of clusters

We can check the status of clusters using the PKS CLI.

.exercise[

    Using ```pks```, check the status of your clusters:
  ```bash
  pks clusters
  ```
  When ```Status``` shows ```succeeded```, our cluster is ready! 
]

---

## Fetching Master IP Address and K8s credentials

Now that our cluster has been created, we'll need to get a couple of things:

*   Fetch the Master IP (this will be used to access our cluster and for any DNS entries).
*   Retrieve the K8s cluster credentials for kubectl access.

.exercise[

    Using ```pks```, get the Master IP and cluster credentials:
  ```bash
  pks cluster <cluster-name>
  pks get-credentials <cluster-name>
  kubectl config use-context <cluster-name>
  ```
  - Be sure to make note of the Master IP address.
  - The IP & credentials will be stored in the ```.kube``` folder of your client.
]