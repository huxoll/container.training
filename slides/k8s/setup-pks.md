# Setting up Kubernetes with PKS

- Before we can get started, we need to:

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
  pks login -a <pksapi.yourcompany.com> -u <userID> -p <password> -k
  pks create-cluster <userID-cluster> --external-hostname <userid-cluster>.yourcompany.com --plan <plan-name> --num-nodes 1
  ```
]

---

## Checking the status of clusters

We can check the status of clusters using the PKS CLI.

.exercise[

    Using `pks`, check the status of your clusters:
  ```bash
  pks clusters
  ```
  When `Status` shows `succeeded`, our cluster is ready! 
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
  - The credentials will be stored in the ```.kube``` folder of your client.
]