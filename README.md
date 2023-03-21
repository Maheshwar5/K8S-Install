# Create a workstation ec2 instance, from this instance we deploy EKS cluster. 

Create IAM Admin user with CLI access, It generates Access Key and Secret Key. Don't keep this in any version control like Github, Gitlab, etc.


Below softwares are mandatory:
- eksctl
- AWS cli v2
- kubectl 



Install AWS CLI, by default AWS instance gets V1, We need to upgrade to V2.
AWS cli steps:
After login to workstation, check aws version: "aws --version"
If the version is aws-cli/1.18.147, upgrade it
Command to upgrade awscli version: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
To upgrade from 1.18 to 2.0: 
Download: curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
This is to upgrade: sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update

This is just to install: sudo ./aws/install




# Install latest eksctl command.
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin

eksctl version

# Install kubectl command maxium one version less than EKS version.
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.24.10/2023-01-30/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/kubectl

eksctl is the popular tool to provision EKS cluster. We make use of spot instances to save the cost.



# Config file to install Cluster: 

eks-config.yaml

# spot-cluster.yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: spot-cluster
  region: ap-south-1

managedNodeGroups:

# `instanceTypes` defaults to [`m5.large`]
- name: spot-1
  spot: true
  ssh:
    publicKeyName: mykeypair


# To create cluster with above config file.
eksctl create cluster --config-file=[file-name].yaml

It will take actually 15 to 20 minutes.

You'll get kubeconfig file after installation:

2023-03-21 11:40:47 [ℹ]  kubectl command should work with "/home/ec2-user/.kube/config", try 'kubectl get nodes'





NOTE: Don't forget to delete the cluster.
eksctl delete cluster --config-file=[file-name].yaml
