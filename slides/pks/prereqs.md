# Pre-requirements

- Be comfortable with the UNIX command line

  - navigating directories

  - editing files

  - a little bit of bash-fu (environment variables, loops)

- Be familiar with Docker and containers.

- Be familiar with Kubernetes management using `kubectl`

- Access to a Pivotal Container Service (PKS) deployment.
    <br/>
    You can also sign-up to take the [Hands-on-Lab](https://my.vmware.com/web/vmware/evalcenter?p=pks-18-hol).

---

class: title

*Tell me and I forget.*
<br/>
*Teach me and I remember.*
<br/>
*Involve me and I learn.*

Misattributed to Benjamin Franklin

[(Probably inspired by Chinese Confucian philosopher Xunzi)](https://www.barrypopik.com/index.php/new_york_city/entry/tell_me_and_i_forget_teach_me_and_i_may_remember_involve_me_and_i_will_lear/)

---

## Hands-on sections

- Much of this workshop is hands-on

- We are going to create & manange a K8s environment with PKS

- We'll also take a closer look at a typical PKS deployment

- You are invited to reproduce all the demos

- All hands-on sections are clearly identified, like the gray rectangle below

.exercise[

- This is the stuff you're supposed to do!

- Go to https://@@GITREPO@@ to fork and view these slides

<!-- ```open @@SLIDES@@``` -->

]

---

class: in-person

## Where are we going to run our containers?

---

class: in-person, pic

![You get a cluster](images/you-get-a-cluster.jpg)

---

class: in-person

## Pivotal Container Service (PKS)

- You will need access to a PKS deployment.

- You can also take the PKS/K8s [Hands on Lab](https://my.vmware.com/web/vmware/evalcenter?p=pks-18-hol)

- You should have a little card with login+password+IP addresses

- You can automatically SSH from one VM to another

- The nodes have aliases: `node1`, `node2`, etc.

---

class: in-person

## Why don't we run containers locally?

- Installing this stuff can be hard on some machines

  (32 bits CPU or OS... Laptops without administrator access... etc.)

- *"The whole team downloaded all these container images from the WiFi!
  <br/>... and it went great!"* (Literally no-one ever)

- All you need is a computer (or even a phone or tablet!), with:

  - an internet connection

  - a web browser

  - an SSH client

---

class: in-person

## SSH clients

- On Linux, OS X, FreeBSD... you are probably all set

- On Windows, get one of these:

  - [putty](http://www.putty.org/)
  - Microsoft [Win32 OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)
  - [Git BASH](https://git-for-windows.github.io/)
  - [MobaXterm](http://mobaxterm.mobatek.net/)

- On Android, [JuiceSSH](https://juicessh.com/)
  ([Play Store](https://play.google.com/store/apps/details?id=com.sonelli.juicessh))
  works pretty well

- Nice-to-have: [Mosh](https://mosh.org/) instead of SSH, if your internet connection tends to lose packets

---

class: in-person, extra-details

## What is this Mosh thing?

*You don't have to use Mosh or even know about it to follow along.
<br/>
We're just telling you about it because some of us think it's cool!*

- Mosh is "the mobile shell"

- It is essentially SSH over UDP, with roaming features

- It retransmits packets quickly, so it works great even on lossy connections

  (Like hotel or conference WiFi)

- It has intelligent local echo, so it works great even in high-latency connections

  (Like hotel or conference WiFi)

- It supports transparent roaming when your client IP address changes

  (Like when you hop from hotel to conference WiFi)

---

class: in-person, extra-details

## Using Mosh

- To install it: `(apt|yum|brew) install mosh`

- It has been pre-installed on the VMs that we are using

- To connect to a remote machine: `mosh user@host`

  (It is going to establish an SSH connection, then hand off to UDP)

- It requires UDP ports to be open

  (By default, it uses a UDP port between 60000 and 61000)
