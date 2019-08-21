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

- From the cloned project, `cd` to the `cli-container` folder and build the Dockerfile:
  ```bash
  cd cli-container
  docker build -t <your-name-of-image> .
  ```

- Run the image:
  ```bash
  docker run -it <your-name-of-image>

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
  pks create-cluster <cluster-name> --external-hostname <external-hostname.com> --plan small --num-nodes 2
  ```
]

---

## Plans & VM Sizing

When you configure plans in the Enterprise PKS tile, you provide VM sizes for the master and worker node VMs.

You select the number of master nodes when you configure the plan.

For worker node VMs, you select the number and size based on the needs of your workload. The sizing of master and worker node VMs is highly dependent on the characteristics of the workload. Adapt the recommendations in this topic based on your own workload requirements.

---

## Master & Worker Node VM Sizing

The master node VM size is linked to the number of worker nodes. If there are multiple master nodes, all master node VMs are the same size.

A maximum of 100 pods can run on a single worker node. The actual number of pods that each worker node runs depends on the workload type as well as the CPU and memory requirements of the workload.

---

## Listing Plans

You can list the current plans through the PKS API with the command <code>pks plans</code> which will return the name, ID and description of each plan configured.  Using the <code>--json</code> flag also returns the number of master and worker nodes configured in JSON format.

.exercise[
Type the following command to list configured plans:
  ```bash
    pks plans
```
This command will return a list of the plans in JSON format:
```bash
    pks plans --json
```
]

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

    _Get the Master IP, obtain the cluster credentials & update the /etc/host file:_
  ```bash
  pks cluster <cluster-name>
  pks get-credentials <cluster-name>
  vi /etc/hosts
  ```
  _The kube credentials will be stored in the ```.kube``` folder of your client.  The following command will set the kubenernetes context to your cluser:_
  ```bash
  kubectl config use-context <cluster-name>
  ```
]

---

## Scaling our Cluster Horizontally

We can scale our cluster horizontally by adding or removing the number of worker nodes.
.exercise[
_Run the following command to add worker nodes to our cluster:_
  ```bash
  pks resize <cluster-name> --num-nodes 3
  ```
_Check the status of the resize:_
  ```bash
  pks cluster <cluster-name>
  ```
_Finally, let's remove the worker nodes we added:_
  ```bash
  pks resize <cluster-name> --num-nodes 2
  ```
]

---

## Scaling our Cluster Vertically

We can also scale our cluster horizontall by:

- Logging into Ops Manager

- Selecting the PKS tile

- Selecting the plan that is in use by the cluster(s) you want to resize

- Selecting the desired VM size of our master and worker nodes

- Applying the changes
