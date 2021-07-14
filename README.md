# Usage

This script will install `asyncssh` onto the ceph base image, and copy my local cephadm binary into image.

Context for `docker build` in `run.sh` is set to `/home/melissali/Downloads` where the Dockerfile is and where .dockerignore is (`*\n!ceph/src/cephadm/cephadm`). My local cephadm binary path is `/home/melissali/Downloads/ceph/src/cephadm/cephadm`, and the destination for cephadm is `/usr/sbin/cephadm` 

1. Run a local docker registry

```
docker run --privileged -d --name registry \
 -p 5000:5000 -v /var/lib/registry:/var/lib/registry \
 --restart=always registry:2
```
2. `chmod +x run.sh`

2. run `sudo ./run.sh melissali-ubuntu:5000`

3. run `sudo ./cephadm --image melissali-ubuntu:5000/ceph/ceph bootstrap ...`


## Adding hosts to the cluster
The local docker registry is insecure: https://docs.docker.com/registry/insecure/ https://github.com/distribution/distribution/issues/1874. Mgr log will show error like `http: server gave HTTP response to HTTPS client`, along with ceph containers not existing.

To prevent docker failures when adding a host to the cluster, create on the host `/etc/docker/daemon.json` with `{ "insecure-registries":["melissali-ubuntu:5000"] }`. Then `sudo systemctl daemon-reload`, `sudo systemctl restart docker
`, `docker info`. Output of docker info should have:

```
Insecure Registries:

melissali-ubuntu:5000
  
127.0.0.0/8
```
