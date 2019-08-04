
class: title

# Our training environment

![SSH terminal](images/title-our-training-environment.jpg)

---

## Our training environment

- Either using your laptop or a VM, you'll need to install the following:

  - Docker Engine
  - Docker Compose
  - Git

---

## The Docker VM


- The VM is created just before the training.

- It will stay up during the whole training.

- It will be destroyed shortly after the training.

---

## What *is* Docker?

- "Installing Docker" really means "Installing the Docker Engine and CLI".

- The Docker Engine is a daemon (a service running in the background).

- This daemon manages containers, the same way that a hypervisor manages VMs.

- We interact with the Docker Engine by using the Docker CLI.

- The Docker CLI and the Docker Engine communicate through an API.

- There are many other programs and client libraries which use that API.

---

## Connecting to your Virtual Machine

You need an SSH client.

* On OS X, Linux, and other UNIX systems, just use `ssh`:

```bash
$ ssh <login>@<ip-address>
```

* On Windows, if you don't have an SSH client, you can download:

  * Putty (www.putty.org)

  * Git BASH (https://git-for-windows.github.io/)

  * MobaXterm (https://mobaxterm.mobatek.net/)

---

## Checking your Virtual Machine

Once logged in, make sure that you can run a basic Docker command:

.small[
```bash
$ docker version
Client:
 Version:       18.03.0-ce
 API version:   1.37
 Go version:    go1.9.4
 Git commit:    0520e24
 Built:         Wed Mar 21 23:10:06 2018
 OS/Arch:       linux/amd64
 Experimental:  false
 Orchestrator:  swarm

Server:
 Engine:
  Version:      18.03.0-ce
  API version:  1.37 (minimum version 1.12)
  Go version:   go1.9.4
  Git commit:   0520e24
  Built:        Wed Mar 21 23:08:35 2018
  OS/Arch:      linux/amd64
  Experimental: false
```
]

If this doesn't work, raise your hand so that an instructor can assist you!
