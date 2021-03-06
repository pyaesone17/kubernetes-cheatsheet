# kubernetes-cheatsheet
Personal Kubernetes cheat sheet for managing micro services 


# Config Map

### Creating config
kubectl create configmap usersvc --from-file=./usersvc.json

### Patching config
kubectl create configmap usersvc --from-file=./usersvc.json --dry-run -o yaml | kubectl replace -f -


# Deployment

### List Deployment
kubectl get deployment

### Delete Deployment
kubectl delete deployment usersvc

### Create Deployment
kubectl create deployment -f ./config-deployment.yaml

### Describe Deployment
kubectl describe pod usersvc

# Service

### Expose service
kubectl expose deployment usersvc --type=LoadBalancer

### Access the service
minikube service usersvc

### Describe service
kubectl describe svc usersvc

### Delete service
kubectl delete svc usersvc

# Cluster

### Create cluster
kops create cluster \
--name=${NAME} \
--master-size=t2.micro \
--node-size=t2.micro \
--state=${KOPS_STATE_STORE} \
--zones=ap-southeast-1a,ap-southeast-1b,ap-southeast-1c \
--ssh-public-key=public-key

### Update cluster to create
kops update cluster ${NAME} --yes

# Debugging
kubectl logs -f <name> [-c <$container>]
  
# Extra

### Create S3 bucket for state store
aws s3api create-bucket --bucket k8s.state.store.url.com --region ap-southeast-1 --create-bucket-configuration LocationConstraint=ap-southeast-1

### Create publich key ($HOME/.ssh/id_rsa)
ssh-keygen
kops create secret --name ${NAME} sshpublickey admin -i ~/.ssh/id_rsa.pub

### List pod with its Node
kubectl get pod -o=custom-columns=NAME:.metadata.name,STATUS:.status.phase,NODE:.spec.nodeName --all-namespaces

### Install dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml

### Get admin password
https://github.com/kubernetes/kops/blob/master/docs/addons.md
kops get secrets kube --type secret -oplaintext

### Create user for the dashboard
https://github.com/kubernetes/dashboard/wiki/Creating-sample-user

### Get token
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')

Furthermore, check

https://kubernetes.io/docs/reference/kubectl/cheatsheet/

### Start minikube with cors
minikube --extra-config apiserver.cors-allowed-origins=["http://*"] start

### Minikube up and running on mac 
https://gist.github.com/kevin-smets/b91a34cea662d0c523968472a81788f7

## Testing local docker registry image from minikube env
minikube ssh -- docker images

## Force pull image to always via CLI
kubectl rolling-update myapp --image=us.gcr.io/project-107012/myapp:5c3dda6b --image-pull-policy Always

## Rolling update hack using the same docker image tag
kubectl patch deployment web -p \ "{\"spec\":{\"template\":{\"metadata\":{\"labels\":{\"date\":\"`date +'%s'`\"}}}}}"
https://github.com/kubernetes/kubernetes/issues/33664

## Pod stack at terminating
Kill pod with force 
kubectl delete pod NAME --grace-period=0 --force

Obtain the node where pod is running
kubectl get pod -o=custom-columns=NAME:.metadata.name,STATUS:.status.phase,NODE:.spec.nodeName --all-namespaces

Restart the docker of the node which has stacked pod
systemctl restart docker.socket docker.service

