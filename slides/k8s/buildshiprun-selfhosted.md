## Self-hosting our registry

For this section we'll be using VMware's Harbor.  Harbor is an enterprise-class registry server that stores and distributes container images. Harbor allows you to store and manage images for use with Enterprise Pivotal Container Service.

Harbor extends the open source Docker Distribution by adding the functionalities usually required by an enterprise, such as security, identity, and management. As an enterprise private registry, Harbor offers enhanced performance and security.

---

## Prequisites to using Harbr

Harbor is usually deployed along side PKS.  Before Harbor can be used to deploy images to you Kubernetes cluster, you will need to be sure Harbor's CA certificate is included in BOSH's certificate trust.

Steps for completing This process are published [here](https://www.virtuallyghetto.com/2018/04/getting-started-with-vmware-pivotal-container-service-pks-part-7-harbor.html).

---

## Using a Private Registry with Docker

- Docker *requires* TLS when communicating with the registry

  - unless for registries on `127.0.0.0/8` (i.e. `localhost`)

  - or with the Engine flag `--insecure-registry`

- Our strategy: Set Docker Engine flag to `--insecure-registry`.

---

## Enabling Docker to use Insecure Registries

To avoid using TLC when communicating with a registry, we can configure Docker to allow by setting the `--insecure-registry` flag.

.exercise[
- Change path to the Docker directory:
  ```bash
  cd /etc/docker
  ```
- If not already present, create the file `daemon.json`, set the flag, save and restart Docker:
  ```bash
  vi daemon.json:
  {
    "insecure-registries" : [ "registryIP:8080" ]
  }
  [esc]:wq
  systemctl daemon-reload
  systemctl restart docker
  ```
]

---

## Connecting to our registry

- Let's test the connection to our registy by logging in with Docker

.exercise[

- View the service details:
  ```bash
  docker login {registry.url.or.IP} -u {username} -p {password}
  ```
- We should see the following:
  ```bash
  WARNING! Using --password via the CLI is insecure. Use --password-stdin.
  WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
  Configure a credential helper to remove this warning. See
  https://docs.docker.com/engine/reference/commandline/login/#credentials-store

  Login Succeeded
  ```
]

---

## Setting our Environment Variable

- For easier automation, we can set an environment variable to point to our Harbor registry

.exercise[

- Set our environment variable:
  ```bash
  REGISTRY={harbor.url.or.IP:port}/{repository}
  ```
]


---

## Testing our Harbor Registry

- We can retag a small image, and push it to the registry

.exercise[

- Make sure we have the busybox image, and retag it:
  ```bash
  docker pull busybox
  docker tag busybox $REGISTRY/busybox
  ```

- Push it:
  ```bash
  docker push $REGISTRY/busybox
  ```

]

---

## Checking our Harbor Registry

- We can verify the image was pushing by logging into our Harbor UI

.center[![harbor busybox](images/harbor-busybox.png)]

---

## Building and pushing our images

- We are going to use a convenient feature of Docker Compose

.exercise[

- Go to the `stacks` directory:
  ```bash
  cd ~/container.training/stacks
  ```

- Build and push the images:
  ```bash
  export REGISTRY
  export TAG=v0.1
  docker-compose -f dockercoins.yml build
  docker-compose -f dockercoins.yml push
  ```

]

Let's have a look at the `dockercoins.yml` file while this is building and pushing.

---

```yaml
version: "3"

services:
  rng:
    build: dockercoins/rng
    image: ${REGISTRY-127.0.0.1:5000}/rng:${TAG-latest}
    deploy:
      mode: global
  ...
  redis:
    image: redis
  ...
  worker:
    build: dockercoins/worker
    image: ${REGISTRY-127.0.0.1:5000}/worker:${TAG-latest}
    ...
    deploy:
      replicas: 10
```

.warning[Just in case you were wondering ... Docker "services" are not Kubernetes "services".]

---

class: extra-details

## Avoiding the `latest` tag

.warning[Make sure that you've set the `TAG` variable properly!]

- If you don't, the tag will default to `latest`

- The problem with `latest`: nobody knows what it points to!

  - the latest commit in the repo?

  - the latest commit in some branch? (Which one?)

  - the latest tag?

  - some random version pushed by a random team member?

- If you keep pushing the `latest` tag, how do you roll back?

- Image tags should be meaningful, i.e. correspond to code branches, tags, or hashes

---

## Checking the Content Harbor

- All our images should now be in the registry

.center[![harbor dockercoins](images/harbor-dockercoins.png)]

*In these slides, all the commands to deploy
DockerCoins will use a $REGISTRY environment
variable, so that we can quickly switch from
the self-hosted registry to pre-built images
hosted on the Docker Hub. So make sure that
this $REGISTRY variable is set correctly when
running the exercises!*