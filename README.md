# AsciiArtify
As 

# Installation (for Ubuntu)
Download and install Docker, k3d
```bash
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```
Let non-root users run Docker (optional)
```bash
$ sudo usermod -aG docker $USER
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
Create a sample index.html file
```bash
$ sudo nano index.html
```
Config:
```bash
"Hello from Pod!"
```
Create a Dockerfile
```bash
$ sudo nano Dockerfile
```
Config:
```bash
FROM nginx:1.23
COPY index.html /usr/share/nginx/html
```

Build, tag and push the image to the container registry
```bash
$ docker image build -t <your-app-name>:v1 .
$ docker image tag <your-app-name>:v1 localhost:5000/<your-app-name>:v1
$ docker image push localhost:5000/<your-app-name>:v1
```
Deploy a pod using the custom built image
```bash
$ sudo nano app-pod.yml
```
Config:
```bash
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: <your-app-name>
  name: <your-app-name>
spec:
  containers:
  - image: dev:5000/<your-app-name>:v1
    name: <your-app-name>
    ports:
    - containerPort: 80
```
```bash
$ kubectl create -f app-pod.yml
```
Expose the pod
```bash
$ sudo nano app-nodeport.yml
```
Config:
```bash
apiVersion: v1
kind: Service
metadata:
  labels:
    run: <your-app-name>
  name: <your-app-name>-nodeport
spec:
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30000
  selector:
    run: app
  type: NodePort
```
```bash
$ kubectl create -f app-nodeport.yml
```
# Curl the webpage to check the pod 
```bash
$ curl http://localhost:30000
```
