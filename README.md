## Pre-requisites
- Kubectl —  https://kubernetes.io/docs/tasks/tools/install-kubectl/
- AWS CLI -  https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html
- Aws iam authenticator — https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html
- eksctl — https://github.com/weaveworks/eksctl

## docker build
docker build -t webapp .

## Run docker image
docker images --filter reference=webapp

##Run the newly built image. The -p 80:80 option maps the exposed port 80 on the container to port 80 on the host system.
docker run -t -i -p 80:80 webapp

##If you are running Docker locally, point your browser to http://localhost/.



## Authenticate to your default registry
IAM authenticator by default uses credentials returned by aws sts get-caller-identity
## New system new - Access Key and Secrete for AWS CLI login

## Create you ecr repo
aws ecr create-repository --repository-name webapp --region ap-south-1
	
## Authenticate your docker to ecr == > gives encrypted password for docker
aws ecr get-login-password --region ap-south-1

## Final Authenticate to ecr
aws ecr --region us-east-1 | docker login -u AWS -p <Above encrytped password> <ACCOUNTID>.dkr.ecr.us-east-1.amazonaws.com/webapp

## docker tag
docker tag webapp:latest <ACCOUNTID>.dkr.ecr.us-east-1.amazonaws.com/webapp:latest

## docker push
docker push <ACCOUNTID>.dkr.ecr.us-east-1.amazonaws.com/webapp:latest

## Create VPC for EKS using stackset -- AWS CLI
aws cloudformation deploy --template-file amazon-eks-vpc-private-subnets.yaml --stack-name my-new-stack

aws cloudformation update-stack --template-url cfn-public-only.yaml --stack-name eks-demo-vpc-stack


## Create our cluster on EKS
change required parameters in cluster.yaml from cfn output
eksctl create cluster -f cluster.yaml --kubeconfig="~/.kube/config"

paste config inside ~/.kube or aws eks update-kubeconfig --region ap-south-1 --name EKS-Demo-Cluster

## Create our deployment
kubectl apply -f deployment.yaml

## Create service
kubectl apply -f service.yaml

## service created
kubectl get services

##  pods of our application are running.
kubectl get pods -o wide

## get nodes command
kubectl get nodes -o wide



