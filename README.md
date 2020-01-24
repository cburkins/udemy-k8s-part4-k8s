# Part 4

##### Installation Instructions from Instructor

NOTE: kubectl seems to be installed already, guessing that Docker for MacOS installed it.

```
brew install kubectl

Download & Install virtualbox for macos

brew cask install minikube
brew install minikube
minikube start (will create VM)

```

##### Checkpoint: Successful cluster

```
$ minikube status
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
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
