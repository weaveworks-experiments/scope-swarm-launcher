# scope-swarm-launcher

[![Docker Hub](https://images.microbadger.com/badges/image/weaveworks/scope-swarm-launcher.svg)](https://hub.docker.com/r/weaveworks/scope-swarm-launcher/)

[Weave Cloud](https://www.weave.works/product/cloud/) Scope launcher is a one-shot service that will provision all Docker Swarm nodes with a Scope container. Because Swarm services don't support privileged mode, the launcher service will run the Scope installer [script](https://github.com/weaveworks/scope/blob/master/scope) and will exit. 

Service create command:

```bash
docker service create --name scope-launcher --mode global --detach \
    --restart-condition none \
    --mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
    weaveworks/scope-swarm-launcher \
    scope launch --service-token=<WEAVE-CLOUD-TOKEN>
```

Stack definition:

```yaml
version: "3.3"

services:
  scope-launcher:
    image: weaveworks/scope-swarm-launcher
    command: scope launch --service-token=${TOKEN}
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    deploy:
      mode: global
        restart_policy:
          condition: none
```

Stack deploy:

```bash
TOKEN=<WEAVE-CLOUD-TOKEN> docker stack deploy -c scope-swarm-launcher.yml weave
```
