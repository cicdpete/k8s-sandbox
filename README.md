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

## events
- `k get events -n prod`

# hello world project
## registry
registry.k8s.io is the production OCI registry 
The infrastructure and backend of the registry are documented here: https://github.com/kubernetes/registry.k8s.io

To list tags:
`skopeo list-tags docker://registry.k8s.io/echoserver`

## create echo server
Echoes back the request.
`k create deployment hello-echo --image=registry.k8s.io/echoserver:1.9 -n prod`

## make available
### create service
`k expose deployment hello-echo --type=LoadBalancer --port=8080 -n prod`

In another terminal:
`minikube service hello-echo -n prod`

### forward load balancer

# Docker
## build docker image
Instead of using the Docker Hub image devopsjourney1/mywebapp, I modified his Dockerfile to address vulnerabilities and reduce the attack surface.

build:
- `cd flaskapp`
- `docker build -t flaskapp:latest .`
- `minikube image load flaskapp:latest`

## image scanning
### install scanner using npm
Installing with npm has the benefit of easy updates.

First install nvm
- Instructions here: https://github.com/nvm-sh/nvm?tab=readme-ov-file#installing-and-updating
- Usage: https://github.com/nvm-sh/nvm?tab=readme-ov-file#usage

View versions available:
- `nvm ls-remote`
- `nvm ls`

To use the latest stable node:
- `nvm install --lts`
- `node version`
- `nvm use --lts`

Sign up for Snyk.

Global install of snyk:
- `npm install -g snyk`

### scan
reference: https://app.snyk.io/org/cicdpete/flow/connect-code
- `snyk auth`
- scan
    - `snyk container test docker-hub-repo/image-name`
    - `snyk container test local-image-name`

## Docker context switching
To inspect docker images within minikube:
- `eval $(minikube docker-env)`

To switch back to the local docker context:
- `eval $(minikube docker-env -u)`

# flaskapp deployment
## deployment
Copy and paste manifest from https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
and modify to suit.

deploy:
- `k apply -f k8s/deployments/flaskapp.yaml`
- `k get deployments`
- `k get pods`
- `k describe pods`

Deployments are an abstraction above ReplicaSets. Deployments allow rolling updates.
- `k get replicaset`

## removal
delete:
- `k delete -f k8s/deployments/flaskapp.yaml`

# grafana
## Deploy
1. Add Helm repo
```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

2. Helm install
```
helm install grafana grafana/grafana \
  -f k8s/deployments/grafana/values.yaml \
  --namespace monitoring \
  --create-namespace
```

3. get admin password by running:
```
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

4. The Grafana server can be accessed via port 3000 on the following DNS name from within your cluster:

   grafana.monitoring.svc.cluster.local

   Get the Grafana URL to visit by running these commands in the same shell:
     ```
     export GRAFANA_SERVICE_IP=$(kubectl get svc --namespace monitoring grafana -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
     http://$GRAFANA_SERVICE_IP:3000
     ```

## to re-deploy
After making changes to values.yaml, redeploy using:
```
helm upgrade grafana grafana/grafana \
  -f k8s/deployments/grafana/values.yaml \
  --namespace monitoring
```

# references
- Installing minikube: https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fdebian+package
- Installing kubectl: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-using-native-package-management
- DevOps Journey https://github.com/devopsjourney1/learn-k8s

