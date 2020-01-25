# Part 4

---

Important: This repo **assumes** that there are several docker images in DockerHub that we can pull down (e.g. cburkins/multi-client)

---

### Breakdown of Part 4 (by sub-directory)

-   01-simplek8s: Two simple YAML files that can be used with "kubectl apply"
-   02-deployment: Rather than just a Pod, use a "Deployment" to control pods

### Installation of MiniKube and Kubectl

##### Installation Instructions from Instructor

NOTE: kubectl seems to be installed already, guessing that Docker for MacOS installed it, but I ended up updating it. Had to delink from /usr/local/bin

```
brew install kubectl

Download & Install virtualbox for macos

brew cask install minikube
brew install minikube
```

##### Checkpoint: Start & Verify Cluster

```
$ minikube start (will create VM)


$ minikube start
* minikube v1.6.2 on Darwin 10.14.6
* Selecting 'hyperkit' driver from existing profile (alternates: [virtualbox])
* Tip: Use 'minikube start -p <name>' to create a new cluster, or 'minikube delete' to delete this one.
* Starting existing hyperkit VM for "minikube" ...
* Waiting for the host to be provisioned ...
! VM may be unable to resolve external DNS records
! VM is unable to access k8s.gcr.io, you may need to configure a proxy or set --image-repository
* Preparing Kubernetes v1.17.0 on Docker '19.03.5' ...
* Launching Kubernetes ...
* Done! kubectl is now configured to use "minikube"

$ minikube status
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

$ kubectl get pods
No resources found in default namespace.
```

##### Config

NOTE: mini-client image needs to be up on DockerHub

##### Examples of k8s Objects

1. StatefulSet
1. ReplicaController
1. Pod
1. Service (configures networking)

##### Examples of apiVersion

Sort of annoying, gives you a different set of objects to use

1. v1 (componentStatus, Endpoints, Event, Pod, etc)
1. apps/v1 (ControllerRevision, StatefulSet)

Common hiearchy of K8s:

1. Node runs a Pod
1. Pod runs a container

Pods run closely related containers

![image](https://user-images.githubusercontent.com/9342308/72815506-c045fe80-3c34-11ea-85b1-755e838dd978.png)

### Create our first Cluster

```
$ kubectl apply -f ./client-pod.yaml
pod/client-pod created

$ kubectl apply -f ./client-node-port.yaml
service/client-node-port created

$ minicube ip
192.168.64.2

# In a browser, navigate to http://192.168.64.2:31515
```

### Troubleshooting minikube

```
minikube stop
minikube delete

# Puts you instide minikube VM (see if you can ping www.google.com)
$ minikube ssh

```

### If minikube can't ping outside world, switch from hyperkit to Virtual Box

```
# To start with virtualbox driver
$ minikube start --vm-driver=virtualbox
```

### Install Virtualbox (which might fail on Mac OS X)

```
$ brew cask install virtualbox
```

If it fails, check SystemPrefs->Security->Allow

If there's no "Allow" checkbox, you can manually add "Oracle" to authorized installers
Reference: https://superuser.com/questions/1436347/cant-allow-virtualbox-installation-in-security-privacy-under-macos

-   Start in Recovery Mode (command-r)
-   From menu at top, select terminal

```
spctl kext-consent add VB5E2TV963
```

### Running ubuntu linux container without exiting

You'll need to loop forever so container image doesn't exit

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: client-pod
    labels:
        component: web
spec:
    containers:
        # Name is arbitrary, helps us to identify logs
        - name: client
          # this image will be downloaded from DockerHub
          image: ubuntu
          # Just spin & wait forever (otherwise container will exit)
          command: ["/bin/bash", "-c", "--"]
          args: ["while true; do sleep 30; done;"]
          # Expose this port to outside world (won't give us access, needs networking service)
          ports:
              - containerPort: 3000
```
