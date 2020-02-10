# Speed up docker build

Not an earth-shattering discovery but a simple nifty time-saving trick when developing docker images that might have escaped your notice.

I'll focus on the most common use-case where Debian/Ubuntu variant is used as the base image in combination
with a separate apt-cacher-ng service. A tool to apt-cacher-ng for RHEL/CentOS yum could be https://github.com/dagwieers/mrepo

## How?

- Spin up an instance of apt-cacher-ng in your local area network on a machine that you can keep online as much as possible
and has at least 50 GB disk space to spare.

- Example docker-compose.yml

```
AptCacherNG:
  image: sameersbn/apt-cacher-ng:latest
  ports:
    - "3142:3142"
  volumes:
    - ./data:/var/cache/apt-cacher-ng
  restart: unless-stopped
```

- Include the following lines near the beginning of all your Dockerfiles

```
ARG http_prox
ARG https_proxy
```

- Let's say apt-cacher-ng is reachable at 10.0.0.1, you run docker build like so:

```
docker build --build-arg http_proxy=http://10.0.0.1:3142 --build-arg https_proxy=http://10.0.0.1:3142 -t testimg .
```

- When apt-cache-ng is unavailable or unreachable, you may run docker build with exactly the same Dockerfiles without `--build-arg` option
