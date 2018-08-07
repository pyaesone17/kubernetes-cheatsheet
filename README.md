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
