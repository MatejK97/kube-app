# Kubernetes cluster app deployment

Lets get it started:

`sudo apt update && sudo apt install awscli -y`

We will need aws to control and manage ec2 and EBS

Lets create a user that will have full access privilage

Lets connect it to our ec2

  `aws configure`


## Lets download and install kubectl first
```
  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" 
  chmod +x ./kubectl 
  sudo mv kubectl /usr/local/bin/
```
Okay lets make sure it works

  `kubectl --help`

## Now we gonna get kops as well
```
  curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
  chmod +x kops
  sudo mv kops /usr/local/bin/kops
```
Lets make sure that works as well

  `kops version`

## We gonna create a cluster with kops

 ` kops create cluster --name=kubevpro.m-kaiser.xyz --state=s3://kobs-state-mkaiser --zones=us-east-1a,us-east-1b --node-count=2 --node-size=t3.small --master-size=t3.medium --dns-zone=kubevpro.m-kaiser.xyz --node-volume-size=8 --master-volume-size=8`
 
And lets update it

  `kops update cluster --name kubevpro.m-kaiser.xyz --state=s3://kobs-state-mkaiser --yes --admin`

And also validate it

  `kops validate cluster --name kubevpro.m-kaiser.xyz --state=s3://kobs-state-mkaiser`

## Last thing we need to get is the aws ebs csi plugin

  `kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/?ref=release-1.27"`

  `kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-ebs-csi-driver`
  
## With all of that ready lets pull the git repo in

  `git pull https://github.com/MatejK97/kube-app.git
`
## Now we can start our pods

  `kubectl create -f ~/kube-apps/`

### Last thing left to do is get the A record of our load balancer and use Route53 to create a A record with our domain
