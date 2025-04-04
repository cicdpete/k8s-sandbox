# k8s-sandbox
k8s deployment using manifests

# command reference

## minikube
`minikube dashboard --url`

## shortcuts
Add to .bashrc: `alias k='kubectl'`

## config
- `k config view`

## namespaces
- `k get namespace`
- `k create -f namespaces/namespace-prod.yaml`
- 'k describe nanmespace prod
- `k config set-context --current --namespace=prod`

## pods
- `k get pods`
- `k get pods -n prod`
- `k get pods --all-namespaces`

# hello world project
## registry
registry.k8s.io is the production OCI registry 
The infrastructure and backend of the registry are documented here: https://github.com/kubernetes/registry.k8s.io

To list tags:
`skopeo list-tags docker://registry.k8s.io/echoserver`

## create echo server
Echoes back the request.
`k create deployment hello-echo --image=registry.k8s.io/echoserver:1.9 -n prod`


# references
- Partially from Kubernetes tutorial, YouTuber: DevOps Journey
- Installing minikube: https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fdebian+package
- Installing kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management

