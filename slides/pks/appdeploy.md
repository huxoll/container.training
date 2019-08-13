# Deploying our Application

---

## Verify we can connect

Let's verify that we can connect to our new K8s Cluster

.exercise[

- _Run this command to see the number of K8s worker nodes provisioned:_
  ```bash
  kubectl get nodes -o wide 
  ```
]

---

## Namespaces

Namespaces with PKS & NSX-T can provide network micro-segmentation on a per-namespace level.<br><br>
 This means Cloud/Platform Operators can isolate applications & services between each other and enforce granular network and security policies provided by the integration with NSX-T.

.exercise[

- _Lets create a new K8s namespace to help separate our new project from others:_
  ```bash
  kubectl create ns yelb
  ```
]

---

## Deployment Spec

Now we need to apply the deployment spec we downloaded earlier in the course.  This describes how we want our application to be connected and deployed.<br><br>
If you take a look at the YAML file, you can see the reference to our namespace that we had created along with the four Docker Containers that will be used.<br><br>
If you do not specify the namespace in the spec, when deploying the application, it will use the "default" namespace.

.exercise[

- _Run the following command to deploy our app using "yelb-lb.yaml" deployment spec:_
  ```bash
  kubectl apply -f yelb-lb.yaml
  ```
]

---

## Deployment Process

Once the deployment has started, we can run the following command to monitor the K8s Pod creation process denoted by the "STATUS" property.<br><br>
Once all four Pods have been successfully created and running, you can hit CTRL+C to exit out.

.exercise[
- _Monitor the deployment process by running the following command:_
  ```bash
  watch -n 1 kubectl get pods --namespace yelb
  ```
]

During the deployment, the required Docker Containers will need to be downloaded and this can take some time depending on size of the containers and the speed of your internet connection.

---

## Acessing the Application

Now that our application has been successfully deployed, note that our deployment spec included a Load Balancer service in front of our application.  During deployment, PKS saw this request and automatically configured an NSX-T Load Balancer and added the virtual servers to the pool with appropriate firewall rules, etc. all done without having to interact with the Cloud/Platform Operator.

.exercise[
- _Retrieve the LB IP with the following command:_
  ```bash
  kubectl get services --namespace yelb
  ```
  _Next, open a browser and enter the IP of our LB (No port needed)_
]

---

## Yelb UI
If everything was setup and deployed correctly, we should see our new Yelb application startup as shown in the screenshot below.

.center[![Yelb Home Page](images/yelb-ui.png)]

You have now successfully deployed your first application onto a PKS managed K8s Cluster!

---

<!-- ## Decomissioning K8s Cluster

When Developers are done working with their K8s Cluster and wish to return the resources, it is simply one command to delete the Cluster.

.exercise[
- _List all clusters to find the one you want to delete:_
  ```bash
  pks clusters
  ```
  _Delete the cluster:_
    ```bash
  pks delete-cluster <cluster-name>
  ```
] -->

## Deleting the Deployment

To set up for our next lab, let's delete the current deployment.

.exercise[
- _Delete the deployment my running the following command:_
  ```bash
  kubectl delete -f yelb-lb.yaml
  ```
]
