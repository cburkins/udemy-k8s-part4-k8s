# Part 4

---

Important: This repo **assumes** that there are several docker images in DockerHub that we can pull down (e.g. cburkins/multi-client)

---

### Breakdown of Part 4 (by sub-directory)

-   **01-simplek8s: Two simple YAML files that can be used with "kubectl apply"**
    This is only a partial deployment of our previous artifically-complex application. Rather than deploy the React front-end client along with Redis etc, this academic example only deploys the React front-end.
-   **02-deployment: Rather than just a Pod, use a "Deployment" to control pods**
    Replaces "client-pod.yaml" with "client-deployment.yaml". Still need "client-node-port.yaml".

### Deviation from Class

NOTE: Instructor used "minikube" to create a single-node kubernetes cluster on the local host. minikube ships with "hyperkit" hypervisor, but I had trouble with that. Minikube VM couldn't ping outside world. Switched from hyperkit to virtualbox hypervisor. Virtualbox refused to install on my Mac. 6 months later, I'm trying these directions again. Booted into Recovery mode on my work MPB, and won't let me open a terminal. So, now switching from Minikube to Docker's Kubernetes. Seems simpler.

### Enable Kubernetes within Docker Desktop

1. Using MacOS Taskbar at top, open "Docker Preferences"
1. Enable kubernetes
1. Check that the cluster is running via "kubectl config view"

    ```
    [cburkin@LocalMac ~/code/udemy-k8s-part4-k8s (master)]
    $ kubectl config view
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: DATA+OMITTED
        server: https://kubernetes.docker.internal:6443
    name: docker-desktop
    contexts:
    - context:
        cluster: docker-desktop
        user: docker-desktop
    name: docker-desktop
    current-context: docker-desktop
    kind: Config
    preferences: {}
    users:
    - name: docker-desktop
    user:
        client-certificate-data: REDACTED
        client-key-data: REDACTED

    ```

### Using our Cluster

##### Create our first pods within the single-node Cluster

NOTE: applies all YAML files in the directory to our cluster master node

```
[cburkin@LocalMac ~/code/udemy-k8s-part4-k8s (master)]
$ kubectl apply -f ./01-simlek8s/
service/client-node-port created
pod/client-pod created

[cburkin@LocalMac ~/code/udemy-k8s-part4-k8s (master)]
$

# In a browser, navigate to http://localhost:31515

```

##### Delete that config

```
[cburkin@LocalMac ~/code/udemy-k8s-part4-k8s (master)]
$ kubectl delete -f ./01-simlek8s/
service "client-node-port" deleted
pod "client-pod" deleted
```

##### Re-create pod & service using deployment

```
[cburkin@LocalMac ~/code/udemy-k8s-part4-k8s (master)]
$ kubectl apply -f ./02-deployment/
deployment.apps/client-deployment created
service/client-node-port created

# In a browser, navigate to http://localhost:31515
```

##### Delete that config

```
[cburkin@LocalMac ~/code/udemy-k8s-part4-k8s (master)]
$ kubectl delete -f ./02-deployment/
deployment.apps "client-deployment" deleted
service "client-node-port" deleted

[cburkin@LocalMac ~/code/udemy-k8s-part4-k8s (master)]
$ kubectl get pods
No resources found in default namespace.

[cburkin@LocalMac ~/code/udemy-k8s-part4-k8s (master)]
$ kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   3h31m
```

##### Appendix: Running ubuntu linux container without exiting

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

##### Appendix: Install Virtualbox (which might fail on Mac OS X)

```
$ brew cask install virtualbox
```

If it fails, check SystemPrefs->Security->Allow

If there's no "Allow" checkbox on "General" tab, you can manually add "Oracle" to authorized installers
Reference: https://superuser.com/questions/1436347/cant-allow-virtualbox-installation-in-security-privacy-under-macos

-   Start in Recovery Mode (command-r)
-   From menu at top, select terminal

```
spctl kext-consent add VB5E2TV963
```

Reboot and install virtualbox

```
# To start with virtualbox driver
$ minikube start --vm-driver=virtualbox
```

##### Checkpoint: Start & Verify Single-Node Cluster

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
