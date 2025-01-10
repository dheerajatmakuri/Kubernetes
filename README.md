# Kubernetes-Zero-to-Hero

Creating this repo with an intent to make Kubernetes easy for beginners. This is a work-in-progress repo.

## Kubernetes Installation Using KOPS on EC2

Follow these steps to set up Kubernetes using kOps on an EC2 instance or your personal laptop.

### Prerequisites

Ensure you have the following dependencies installed:
- Python3
- AWS CLI
- kubectl

### Install Dependencies

1. Add the Kubernetes APT repository:
    ```sh
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
    sudo apt-get update
    sudo apt-get install -y python3-pip apt-transport-https kubectl
    pip3 install awscli --upgrade
    export PATH="$PATH:/home/ubuntu/.local/bin/"
    ```

### Install kOps

2. Download and install kOps:
    ```sh
    curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
    chmod +x kops-linux-amd64
    sudo mv kops-linux-amd64 /usr/local/bin/kops
    ```

### IAM Permissions

3. Provide the following IAM permissions to your user. If you are using an admin user, these permissions are available by default:
    - AmazonEC2FullAccess
    - AmazonS3FullAccess
    - IAMFullAccess
    - AmazonVPCFullAccess

### Configure AWS CLI

4. Set up AWS CLI configuration on your EC2 Instance or laptop:
    ```sh
    aws configure
    ```

### Kubernetes Cluster Installation

Follow these steps carefully and read each command before executing.

5. Create an S3 bucket for storing the kOps objects:
    ```sh
    aws s3api create-bucket --bucket kops-abhi-storage --region us-east-1
    ```

6. Create the cluster:
    ```sh
    kops create cluster --name=demok8scluster.k8s.local --state=s3://kops-abhi-storage --zones=us-east-1a --node-count=1 --node-size=t2.micro --master-size=t2.micro --master-volume-size=8 --node-volume-size=8
    ```

7. Edit the cluster configuration to ensure resources fit within the free tier (if applicable):
    ```sh
    kops edit cluster demok8scluster.k8s.local
    ```

8. Build the cluster:
    ```sh
    kops update cluster demok8scluster.k8s.local --yes --state=s3://kops-abhi-storage
    ```
    This will take a few minutes to complete.

9. Verify the cluster installation:
    ```sh
    kops validate cluster demok8scluster.k8s.local
    ```
