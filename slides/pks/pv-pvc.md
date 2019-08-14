# Persistent Volumes & Persistent Volume Claims

Let's look at options for configuring PKS on vSphere to support stateful apps using PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs).

---

## Considerations for Running Stateful Apps in Kubernetes

There are several factors to consider when running stateful apps in Kubernetes:

- _Pods are ephemeral by nature._ Data that needs to be persisted must be accessible on restart and rescheduling of a Pod.

- _When a Pod is rescheduled, it may be on a different host._ Storage must be available on the new host for the Pod to start gracefully.

- _The app should not manage the volume and data._ The underlying infrastructure should handle the complexity of unmounting and mounting.

- _Certain apps have a strong sense of identity._ When a container with a certain ID uses a disk, the disk becomes tied to that container. If a Pod with a certain ID gets rescheduled, the disk associated with that ID must be reattached to the new Pod instance.

---

## Persistent Volume Provisioning Support in Kubernetes

Kubernetes provides two ways to provision persistent storage for stateful applications:

- _Static provisioning:_ A Kubernetes administrator creates the Virtual Machine Disk (VMDK) and PVs. Developers issue PersistentVolumeClaims (PVCs) on the pre-defined PVs.
    
- _Dynamic provisioning:_ Developers issue PVCs against a StorageClass object. The provisioning of the persistent storage depends on the infrastructure. With Enterprise PKS on vSphere, the vSphere Cloud Provider (VCP) automatically provisions the VMDK and PVs.

PVs can be used with two types of Kubernetes workloads:

- Deployments
- StatefulSets

---

## vSphere Support for Static and Dynamic PVs

With Enterprise PKS on vSphere, you can choose one of two storage options to support stateful apps:

- vSAN datastores

- Network File Share (NFS) or VMFS over Internet Small Computer Systems Interface (iSCSI), or fiber channel (FC) datastores

In Enterprise PKS, an availability zone (AZ) corresponds to a vSphere cluster and a resource pool within that cluster. 

A resource pool is a vSphere construct that is not linked to a particular ESXi host. Resource pools can be used in testing environments to enable a single vSphere cluster to support multiple AZs. 

As a recommended practice, deploy multiple AZs across different vSphere clusters to afford best availability in production.

---

## Dynamic PersistentVolumes (PVs)

For this lab, we'll focus on Dynamic PVs.  To dynamically create a PV, the K8s cluster administrator can define one or more storage classes that map to the type of storage required.  For example:

- The disk might be `thin`
- The disk might be `thick`
- The disk might be `eager zero'd this`
- A specific datastore may be required

If vSphere storage policies have been configured, The storage policy name can be referenced as well.

The vSphere Cloud Provider also services vSAN storage parameters such as Disk Stripes, IOps limits & Cache Reservation.

---

## PersistentVolumeClaims (PVCs)

Once the Storage Classes are defined, the K8s cluster administrator can create pools of storage by creating PVCs.

The claim defines the actual virtual disk to be created by referencing the storage class and providing additional parameters such as:

- Disk Size
- Access Modes

In Dynamic Volume creation, the vmdk is automatically created and bound to the PVC as part of the PVC creation.  App developers define Pod specs that create Pods and mount the volumes created through the PVC.

If a Pod fails, the disk will persist.  If a replica set has been defined on the Pod, it will be recreated and the disk will be mounted on its file system.

_Let's see how this works!_

---

## Create the StorageClass YAML

We'll start by creating a `StorageClass`.  The only parameter we'll define here is to `thin provision` the disk.

.exercise[

- _Create a file called "redis-sc.yaml" with the following:_

    ```bash
    kind: StorageClass
    apiVersion: storage.k8s.io/v1
    metadata:
        name: thin-disk
    provisioner: kubernetes.io/vsphere-volume
    parameters:
        diskformat: thin
    ```
]

---

## Create the PersistentVolumeClaim (PVC) YAML

Now let's create a PVC.  We will use the "thin-disk" storage class and create a 2 GB volume.

.exercise[
- _Create a file called "redis-worker-claim.yaml" with the following:_

    ```bash
    kind: PersistentVolumeClaim
    apiVersion: v1
    metadata:
        name: redis-worker-claim
        annotations:
            volume.beta.kubernetes.io/storage-class: thin-disk
    spec:
        accessModes:
            - ReadWriteOnce
        resources:
            requests:
                storage: 2Gi
    ```
]

---

## Apply the YAML Files

Using the YAML files we created, we'll create a StorageClass and PVC

.exercise[

- _Run the following command to create the StorageClass:_

    ```bash
    kubectl apply -f redis-sc.yaml
    ```
- _Run this command to create the PVC  (Be sure to include the namespace):_

    ```bash
    kubectl apply -f redis-worker-claim.yaml --namespace=yelb
    ```
]

---

## Verify the PVC

Let's verify the PVC has been bound to a vSphere volume.

.exercise[

- _Run the following command to verify the PVC binding to a vSphere volume.  You can also verify the size and storage class:_

    ```bash
    kubectl get pvc --namespace=yelb
    ```
- _You should se something similar to the below results:_

    ```bash
    NAME                 STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
    redis-worker-claim   Bound    pvc-41eeb414-bd74-11e9-8072-00505688879c   2Gi        RWO            thin-disk      72m
    ```

- _More info can be found by running `kubectl describe pvc/redis-worker-claim`_
]

---

class:pic

## Verify vmdk Creation in vCenter

.center[You can also view the vmdk in vCenter.]

![vCenter VMDK](images/vcenter-pvc.png)

---

## Download the Modified Deployment Manifest

At this point, we would need to add the "volumeMounts" and "volumes" parameters to our "redis-server" deployment spec, however, this has been done for you!

_Download the modified deployment manifest from:_

`https://raw.githubusercontent.com/dahr/yelb/master/yelb-lb-pvc.yaml`

Take a minute to view the modifications that were made.



---

class: pic

## Modify the Application Deployment Spec

![dep-spec](images/dep-spec.png)

---

## Apply the New Deployment Manifest

We'll apply the new deployment manifiest which will attach the volume to our "redis-server" pod.

.exercise[

- _Apply the new deployment manifest:_

    ```bash
    kubectl apply -f yelb-lb-pvc.yaml
    ```
]

---

## Adding Data & Deleting Pod

Now, let's open the app and create some data.  Since we've added a persistent volume, our data will be available even if we delete the "redis" Pod.  Once we've added some data, let's delete the "redis" pod.

.exercise[

- _List the pods to find the "redis" pod:_

    ```bash
    kubectl get pods --namespace=yelb
    ```
- _Now let's delete the "redis" pod:_

    ```bash
    kubectl delete pod/redis-server-xxxxxxxxxx --namespace=yelb
    ```
- _Remember to include the namespace!_
]

---

## Data Persistence

When you list the pods again, you will notice the "redis" pod is terminating and a new pod is being created in it's place.  Once the new pod is available, you can re-launch the app and see that the data has been persisted!

.exercise[

- _List the pods to observe the terminated & recreated pod pod:_

    ```bash
    kubectl get pods  --namespace=yelb
    ```
- _Now let's re-launch the app.  You will see the data is still there!_

]

