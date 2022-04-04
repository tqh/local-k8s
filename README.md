# local-k8s

## Setup
### Setting up local Kubernetes Cluster / Docker Env

```
brew install minikube skaffold kubectl
brew install docker hyperkit #Only needed if you don't have/want Docker for Desktop

# Config for Minikube (runs Kubernetes in a HyperKit Virtual Machine)
# (HyperKit is the Virtual Machine used by Docker for Desktop as well)
minikube config set driver hyperkit
minikube config set cpus 4
minikube config set memory 8192
minikube config set disk-size 100GB

minikube start 
# OR If your network needs lower MTU: http://www.letmecheck.it/mtu-test.php
# Otherwise building or pulling docker images may take forever
# minikube delete old Virtual
minikube start --docker-opt network-control-plane-mtu=1400 --docker-opt mtu=1400

## Kubernetes is running inside Minikube
```

### Autocompletion in ZSH
In ~/.zshrc add
```
[[ /usr/local/bin/kubectl ]] && source <(kubectl completion zsh)
[[ /usr/local/bin/minikube ]] && source <(minikube completion zsh)
```

### Useful commands
```
minikube ssh # ssh's into the linux virtual machine

eval $(minikube docker-env) # Sets up the docker client to talk to the docker daemon inside minikube in this terminal
## Note that this may cause confusion as you can have Docker For Desktop as well
## So that you see different results (like docker images) depending if you talk to Minikube docker or Docker for Desktop

minikube pause #Pauses Kubernetes inside minikube to save energy 
minikube unpause

minikube stop #Stops the Virtual machine
minikube start

minikube delete

kubectl help # Help
kubectl options # Help about options

kubectl get all # List all the things inside the current namespace

kubctl get ns # List namespaces

kubectl -n <namespace> get all # List all in a specific namespace

kubectl logs -f <pod> #tail logs for a pod

kubectl port-forward <pod> <port> #Access a port on a remote machine locally
```

## Kubernetes Basics Course
https://kubernetes.io/docs/tutorials/kubernetes-basics/

### Tricks
Kubernetes DNS caches responses for 30 seconds, even if it is a **NOT FOUND** response. This may cause long startup as it waits for the cache to have the real name. If we reduce the negative cache to 2s it helps a lot. 
```
kubectl -n kube-system edit configmap coredns
```
Change the line 
```
cache 30
```
To
```
cache 30 {
  denial 9984 2
}
```
and save.  
This will cache negative responses for 2 seconds. 9984 is the max no of items to cache and the default value.
After that restart coredns, by deleting the pod so a new one will start up.
```
kubectl -n kube-system delete pod coredns-<some hash> #Can probably be autocompleted with tab in terminal
```

## Skaffold

### JIB example: 
https://github.com/GoogleContainerTools/skaffold/tree/main/examples/jib


