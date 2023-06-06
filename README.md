# AsciiArtify

# Installation (for WSL)
Download and install Docker, k3d
```bash
$ $ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```

```bash
$ curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash
```
```bash
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
$ sudo install kubectl /usr/local/bin/kubectl
```

Create configuration file
```bash
$ sudo nano config.yml
```
Config:
```bash
apiVersion: k3d.io/v1alpha4
kind: Simple
metadata:
  name: dev
servers: 1
agents: 2
image: rancher/k3s:v1.25.2-k3s1
ports:
- port: 30000-30100:30000-30100
  nodeFilters:
  - server:*
registries:
  create:
    name: dev
    host: 0.0.0.0
    hostPort: "5000"
options:
  k3s:
    extraArgs:
    - arg: --disable=traefik
      nodeFilters:
      - server:*
```

Create the cluster using the configuration file

```bash
$ k3d cluster create --config config.yml
```


Build, tag and push the image to the container registry
```bash
$ docker image build -t <your-app-name>:v1 .
$ docker image tag <your-app-name>:v1 localhost:5000/<your-app-name>:v1
$ docker image push localhost:5000/<your-app-name>:v1
```