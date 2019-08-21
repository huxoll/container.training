# Harbor

Harbor is an Enterprise-class container registry that customers can run within their own Datacenter to securely store and provide access to container images used by their development teams.<br><br>
The process of deploying Harbor is similiar to PKS. You will need to download the Harbor Tile from Pivotal Network, import that into Ops Manager and then configure and deploy using the same interface.

.center[![harbor logo](images/harbor_logo.png)]

This course assumes you already have Harbor installed in your environment.

---

## Pulling Images

Let's pull down some images from Docker Hub that we can use to push to Harbor.

.exercise[
_Run the following commands to pull down images:_
  ```bash
    docker pull mreferre/yelb-ui:0.3
    docker pull mreferre/yelb-appserver:0.3
    docker pull mreferre/yelb-db:0.3
    docker pull redis:4.0.2
  ```
]

---

## Logging In

Harbor extends the open source Docker Distribution by adding the functionalities usually required by an enterprise, such as security, identity, and management.  Therefore, you will use the Docker CLI just as you would with Docker Hub.

.exercise[
_Log into Harbor using the FQDN of your deployment:_
  ```bash
  docker login <fqdn-of-harbor-deployment.com>

  Username: <your-user-name>
  Password: ********
  ```
]

---

##  Tagging Images

Before we can push our images to Harbor, we must tag them with:
- The DNS name of our Harbor instance
- The project name ("library" is the default)
- The name of our conainter
- The version of our container


 .exercise[
_Use the following commands to tag our images:_
  ```bash
docker tag mreferre/yelb-ui:0.3 <fqdn-of-harbor.com>/library/yelb-ui:0.3
docker tag mreferre/yelb-appserver:0.3 <fqdn-of-harbor.com>/library/yelb-appserver:0.3
docker tag mreferre/yelb-db:0.3 <fqdn-of-harbor.com>/library/yelb-db:0.3
docker tag redis:4.0.2 <fqdn-of-harbor.com>/library/redis:4.0.2
  ```
]

---

## Pushing Images

Now we are ready to push these images into our private registry.

 .exercise[
_Running the following commands simply reference the tag names we had used in the previous slide:_
  ```bash
docker push <fqnd-of-harbor.com>/library/yelb-ui:0.3
docker push <fqnd-of-harbor.com>/library/yelb-appserver:0.3
docker push <fqnd-of-harbor.com>/library/yelb-db:0.3
docker push <fqnd-of-harbor.com>/library/redis:4.0.2
  ```
]

---

## Redeploying our Application

We can now re-deploy our application but rather than using an image from Docker's registry, which requires internet access, we can actually refer to our Harbor instance.

First, we'll download an updated YAML file that references the new registry, then we'll re-deploy our app.

 .exercise[
_Updated yaml is included in the yelp app folder:_

```bash
cd yelb
cat yelb-lb-harbor.yaml
```
_Now let's re-deploy our app:_

```bash
kubectl apply -f yelb-lb-harbor.yaml
```
]

---

## Key Features

- Replication: Harbor supports images replication from one Harbor instance to another.

- LDAP: Harbor integrates with enterprise LDAP/AD systems for user authentication and management.

- Labels: Harbor provides labels to isolate image resources globally or at the project level.

- Helm Charts: Harbor provides management of Helm charts isolated by projects and controlled by RBAC.

- Integrated Authentication: Harbor can share UAA authentication with Pivotal Application Service (PAS) and Enterprise PKS.

- RBAC: Users can have different permissions for the images in different projects.


---

## Key Features (cont)

- Policy-Based Image Replication: Images can be synchronized between multiple registry instances with auto-retry on errors, offering support for load balancing, high availability, multi-datacenter, hybrid, and multi-cloud scenarios.

- Vulnerability Scanning: Harbor uses Clair (CoreOS) to scan images regularly and warn users of vulnerabilities.

- Image Deletion and Garbage Collection: Images can be deleted and their space can be recycled.

- Notary: Image authenticity can be ensured by using Docker Notary.

- Auditing: All the operations to the repositories are tracked.

- RESTful API: RESTful APIs for most administrative operations, easy to integrate with external systems.
