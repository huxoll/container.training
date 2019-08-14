# Viewing the K8s Dashboard UI

While consuming one of the PKS managed K8s Cluster, you may want to access the built-in K8s Web UI Dashboard (which is installed by default as part of the K8s setup by PKS).

However, the K8s Dashboard currently does not support OAuth Tokens which prevents us from easily accessing the UI.

Fortunately, there is a workaround which involves leveraging `kube-proxy` to proxy to the Dashboard UI of the K8s Master Node which allows  access from our desktop machine.

---

## SSH Tunnel

If your working machine does not have `kubectl` installed, you can SSH Tunnel to a CLI machine that does.  

- On a Mac or Linux machine, you can simply run `ssh root@pks-client -L 8001:127.0.0.1:8001 -N`.  

- Using _Putty_ on Windows, you can configure the SSH Tunnel to your CLI machine.

---

class: pic

## Putty SSH Tunnel

![putty tunnel](images/putty-tunnel.png)

---

## Running the K8s Proxy

On your CLI machine (or if your working machine has `kubectl` installed), start `kube-proxy` by running `kubectl proxy`.  You should see the following result:

```bash
root@pks-client:~# kubectl proxy
Starting to serve on 127.0.0.1:8001
```

---

## Access the K8s Dashboard

To access the K8s Dashboard, open a browser and connect to 'http://localhost:8001/ui' which should take you to the login page. 

From here, you will need a copy of the specific K8s Cluster configuration file _(stored in ~/.kube/config which can be pulled using pks get-credentials [NAME-OF-PKS-CLUSTER])_ and provide.

---

class: pic

## K8s Dashboard

![dashboard K8s](images/dashboard-kubecfg.png)

---

## Access the Cluster Dashboard

After signing in with the K8s Configuration file, you should be taken the dashboard for your specific K8s Cluster. 

If you do not see any of your pods, make sure to toggle the Namespace from the system "Default".

---

class: pic

##  Cluster Dashboard

![dashboard cluster](images/dashboard-cluster.png)

