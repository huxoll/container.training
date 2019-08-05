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
  kubectl create namespace yelb
  ```
]

---

## Deployment Spec

Now we need to apply the deployment spec we downloaded earlier in the course.  This describes how we want our application to be connected and deployed.<br><br>
If you take a look at the YAML file, you can see the reference to our namespace that we had created along with the four Docker Containers that will be used.<br><br>
If you do not specify the namespace in the spec, when deploying the application, it will use the "default" namespace.

.exercise[

- _Run the following command to deploy our app using "yelb.yaml" deployment spec:_
  ```bash
  kubectl apply -f yelb.yaml
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

Now that our application has been successfully deployed, we need to identify which of the K8s Worker Nodes is hosting our UI frontend so that we can access the application using our browser.  Be sure to note the IP address.  We'll use port 30001 which was defined in our Deployment Spec.

.exercise[
- _Get the unique Pod name for our yelb-ui which you can do so by running the following command_
  ```bash
  kubectl get pods --namespace yelb
  ```
  _Run the following command and specify the name of your UI Pod name to retrieve more details about this Pod:_
    ```bash
  kubectel describe pod yelb-ui-<UUID-of-pod> --namespace yelb
  ```
]

---

## Yelb UI
Open a web browser to the IP Address found by running the previous command and using port 30001.  f everything was setup and deployed correctly, we should see our new Yelb application startup as shown in the screenshot below.

.center[![Yelb Home Page](images/yelb-ui.png)]

You have now successfully deployed your first application onto a PKS managed K8s Cluster!  However, it is not a general best practice to expose a particular Worker Node for access which relies on a NAT, especially when we need to scale our application up or down based on demand.

---

## External Load Balancing

We can deploy an External Load Balancer in front of our application.  This will be specified in our application deployment spec.<br><br>
During deployment, PKS will see this request and automatically configure an NSX-T Load Balancer and add the virtual servers to the pool with appropriate firewall rules, etc. all done without having to interact with the Cloud/Platform Operator. Lets see this in action.
.exercise[
- _Download the load balancer K8S deployment spec called yelb-lb.yaml file:_
  ```bash
  git clone https://github.com/lamw/vmware-pks-app-demo/blob/master/yelb-lb.yaml
  ```
  _Deploy our new Yelb application which will include a Load Balancer:_
    ```bash
  kubectl apply -f yelb-lb.yaml
  ```
]

---

class: pic

## Comparing our YAML Deployment Files

If we compare the two YAML files, we can see the only difference is adding the LoadBalancer type which instructs K8s to request a Load Balancer.<br><br>

![Yaml LB Deployment](images/yaml-lb.png)

---

## Accessing App Through the Load Balancer

Once the new application has been deployed. We can retrieve the Load Balancer IP.

.exercise[
- _Retrieve the LB IP with the following command:_
  ```bash
  kubectl get services --namespace yelb
  ```
  _Next, open a browser and enter the IP of our LB (No port needed)_
]

Obviously, for Production deployment, you would want to add a DNS entry for this IP Address so that your end users can reach the application with a friendly name rather than IP. 

---

## Decomissioning K8s Cluster

When Developers are done working with their K8s Cluster and wish to return the resources, it is simply one command to delete the Cluster.

.exercise[
- _List all clusters to find the one you want to delete:_
  ```bash
  pks clusters
  ```
  _Delete the cluster:_
    ```bash
  pks delete-cluster <NAME-OF-PKS-CLUTER>
  ```
]
