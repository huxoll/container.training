# Our sample application

- For this course, we'll use an application called [Yelb](http://www.it20.info/2017/07/yelb-yet-another-sample-app/) written by [Massimo Re Ferre'](https://twitter.com/mreferre) & [Andrea Siviero](https://www.linkedin.com/in/andreasiviero/)
  which contains the following four services: 
  
  - UI Frontend
  - Application Server
  - Database Server
  - Caching Service using Redis. 

.exercise[

- Clone the repository on Docker host and switch to `yelb` director:
  ```bash
  git clone @@GITREPO@@
  cd yelb/
  ```

- Note the K8s Manifests (yaml) that are also included in the folder.
`yelb-lb.yaml`
`yelb-lb-pvc.yaml`

]

---

class: pic

## What's this application?

- Yelb is a crowd sourced review forum for healthy food recommendations!

![yelb ui](images/yelb-ui.png)

---

class:pic

## Yelb Architecture

![Yelb Architecture](images/yelb-architecture.png)

---

## Application Deployment

 - These services have been packaged up as Docker Containers and you want to be able to quickly deploy this to Production and not have to worry about individual container deployments.
 
- This is why you prefer K8s which is a robust container scheduler platform to do this for you.

- As Cloud/Platform Operators, we'll be able to quickly provision a new K8s Cluster and the K8s configuration file and endpoint so that our application can be deployed immediately.

- So let's get started!
