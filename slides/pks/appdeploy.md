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

## Deployment Deployment Process

Once the deployment has started, we can run the following command to monitor the K8s Pod creation process denoted by the "STATUS" property.<br><br>
Once all four Pods have been successfully created and running, you can hit CTRL+C to exit out.

.exercise[

- _Monitor the deployment process by running the following command:_
  ```bash
  watch -n 1 kubectl get pods --namespace yelb
  ```
]


