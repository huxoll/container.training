# Cluster and Application Monitoring

When it comes to monitoring, different consumers of PKS will care about different things.  For example, the platform team will be concerned with the underlying infrastructure whereas developers want to know how their applications is doing.  For this section, we'll touch on three key areas of monitoring and solutions for each:

- Logging

- Infrastructure Monitoring

- Application Monitoring

---

## PKS Logging

Complete end-to-end logging is a must-have for any enterprise.  Remote syslog can be configured throughout the PKS stack.  Of course, we recommend vRealize Log Insight for an on-premise, log management solution.

- For PKS, you can configure BOSH to forward its logs by configuring the <code>Syslog</code> tab under the BOSH tile in Ops Manager.

.center[![Bosh Tile](images/bosh-tile.png) ![Syslog Config](images/syslog-config.png)]

---

## NSX-T Logging

vRealize Log Insight can also be configured to accept a log stream from NSX-T.  Installing the NSX-T Content Pack provides you with several useful dashboards specific to an NSX-T deployment.

- To send NSX-T Logs to vRLI, you will need to use the NSX CLI after you SSH into each of the NSX Components (Manager, Controllers & Edges):

.center[<code>set logging-server {vrli-ipaddress} proto udp level info</code>]

---

## Kubernetes & Application Logging

Additionally, vRLI can accept log streams from K8s cluster and the applications that reside inside.

Since _Fluentd_ is the Kubernetes recommended data collector, VMware recommends installing the data collector on your K8s nodes (which requires Ruby).  _Fluentd_ is open source and very flexible as you can send logs via many different output plugins and protocols.

The the vRLI and K8s metadata plugins.  After configuring _Fluentd_ to send logs to vRLI, you can start the _Fluentd_ service and begin streaming logs.

A step-by-step guide from VMware's own Nico Guerrera can be found [here](https://blogs.vmware.com/management/2019/04/forwarding-kubernetes-logs-to-vrealize-log-insight-via-fluentd.html).

---

## Infrastructure Monitoring

For Cloud Admins/Platform Operators, vRealize Operations Manager (vROPs) will be the tool of choice which many of our customers are already familiar with.  vROps provides analytics, capacity management and alerting for all of your underlying compute, storage and networking infrastructure.  This information can be trended over time and provide help proactive identify any anomalies within the infrastructure before they arise. 

There are a number of Management Packs that can be used to provide easy to consume and out of the box dashboards such as vSphere, NSX-T, vSAN and PKS.  To set up vROps to monitor PKS and K8s, you'll need the following information:

- K8s server endpoint
- PKS cluster name
- K8s username
- K8s token

This information is added to the container management pack for each PKS custer you plan to monitor.

---

## Application Monitoring

VMware Wavefront is a leading SaaS-based, metrics and monitoring solution for Cloud Native Applications.  Not only does it give us deeper metrics about our K8s infrastructure (cluster, pod, namespaces and containers), but it can also be used to help developers instrument their application for metric collection and monitoring and provide a complete end-to-end view.

Wavefront proxy, proxy service and heapster all run as a Pod within the PKS managed K8S Cluster that you to monitor. Since the Wavefront proxy is running within the K8s Cluster, you will need to make sure the nodes can actually connect out to the internet and reach Wavefront. If you do not have direct internet access (which most customers do not in a Production environment), you can setup a proxy host which does have access.

---

## The Complete Monitoring Toolset

Together, vRealize Log Insight, vRealize Operations and Wavefront provide end-to-end monitoring of vSphere, NSX-T, PKS, K8s and your application.

.center[![Monitoring Toolset](images/pks-monitoring.png)]
