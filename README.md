# AWS EKS
## 📦 Demo 1
This demo is part of **Module 11: Kubernetes on AWS (EKS)** in the **TWN DevOps Bootcamp**. The objective is to provision a production-grade **Amazon EKS cluster** with a managed **Node Group**, enabling scalable Kubernetes workloads on AWS.

## 🚀 Technologies Used
- **Amazon EKS**: Amazon Elastic Kubernetes Service.
- **Amazon EC2**: Compute Instances used for worker nodes.
- **I AM**: AWS identity service to manage access and secure permissions.
- **kubectl**: CLI to interact with Kubernetes.
- **AWS CLI**:Interface for maneging AWS services
- **CloudFormation**: To manage VPC template.
  
## 📋 Prerequisites
- Ensure you have a AWS Account.
- Ensure AWS CLI is installed and configure.
- Kubectl is installed and configured to connect to the kubernetes cluster.
  


## 📌 Objective
Set up a fully managed Kubernetes environment using **Amazon EKS** and deploy a **Node Group** of EC2 instances to serve as worker nodes.

## 🎯 Features
- Configure IM Roles.
- Create a VPC using CloudFormation template.
- Create EKS cluster (Control Plane Nodes).
- Create NodeGroup for workers node.
- Attache the NodeGroup to the EKS Cluster.
- Configure Autoscaling Group for eorker nodes.
- Deploy a sample application to EKS Cluster.


## 🏗 Project Architecture

<img src=""/>

## ⚙️ Project Configuration
### Creating IM Role for EKS Cluster
1. Access AWS console.
2. Search for the IAM service and navigate to the Roles option.
3. Click Create Role.
4. Configure the Role setting the trusted entity type and the use case. In this case, the trust entity is an AWS Service. The use case is foor the EKS cluster.
5. Click Create

### Using CloudFormation Template (IaC) to Create the VPC
1. Search For the CloudFormation service.
2. Click Create stack and choose standard configuration.
3. Prepeare template and specify from Amazon S3 URL. The template used in this example is found in the AWS userguide.
   [AWS EKS userguide](https://docs.aws.amazon.com/eks/latest/userguide/creating-a-vpc.html)
5. Specify the Stack name and the networking confifuration. For this example we are using the default subnets created by AWS.
6. Click Create.
7. This tempate creates 22 resources.

### Creating EKS Cluster for Control Plane Nodes
1. Search EKS service.
2. Click Create EKS Cluster.
3. In the configuration section set custom configuration, name the custer and set the role previously created.
4. Configure the Cluster Access by allowing Cluster administration access and  selecting the cluster authentication mode as EKS API and ConfigMap.
5. In the Networking section, select the VPC, subnets and security group created with CloudFormation.
6. Select the Cluster Endpoint Access. The best practice is to select public and private. This allows to connec to access the endpoint outside of the VPC, and the internal resources can access privatey from the VPC.

### Getting the config file to connect our kubctl with the EKS Cluster.
1. Go to the CLI use aws configure list to list the configuration details.
2. Create the kubeconfig file locally. This file contains the cluster information to be able to access the cluster using aws eks update-kubeconfig <cluster name>:
   ```bash
   aws eks update-kubeconfig eks-cluster-test
   ```

