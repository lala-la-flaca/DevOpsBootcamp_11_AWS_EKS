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
- Create a VPC using a CloudFormation template.
- Create EKS cluster (Control Plane Nodes).
- Create a NodeGroup for the workers node.
- Attach the NodeGroup to the EKS Cluster.
- Configure Autoscaling Group for worker nodes.
- Deploy a sample application to the EKS Cluster.


## 🏗 Project Architecture

<img src=""/>

## ⚙️ Project Configuration
### Creating IM Role for EKS Cluster
1. Access AWS console.
2. Search for the IAM service and navigate to the Roles option.
3. Click Create Role.
4. Configure the Role setting the trusted entity type and the use case. In this case, the trust entity is an AWS Service. The use case is for the EKS cluster.
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
3. In the configuration section, set custom configuration, name the custer and set the role previously created.
4. Configure the Cluster Access by allowing Cluster administration access and  selecting the cluster authentication mode as EKS API and ConfigMap.
5. In the Networking section, select the VPC, subnets and security group created with CloudFormation.
6. Select the Cluster Endpoint Access. The best practice is to select public and private. This allows to connec to access the endpoint outside of the VPC, and the internal resources can access privatey from the VPC.

### Getting the config file to connect our kubctl with the EKS Cluster.
1. Go to the CLI and use aws configure list to list the configuration details.
2. Create the kubeconfig file locally. This file contains the cluster information to be able to access the cluster using aws eks update-kubeconfig <cluster name>:
   ```bash
   aws eks update-kubeconfig eks-cluster-test
   ```
3. Verify the kubeconfig file.
4. Verify the namespaces that were created.
5. Verify the cluster information.

### Creating Role for Worker Nodes (kubelet)
1. Navigate to the IAM service and go to the role section.
2. Create a new role.
3. Select AWS service as the trusted entity type and the use case EC2. We are create the role to allow the EC2 instances (worker nodes) to vommunicate with AWS services, EKS cluster.
4. We add  permissions to the role: AmazonEKSWorkerNodePolicy, AmazonEC2ContainerRegistryReadOnly, AmazonEKS_CNI_Policy.

### Creating NodeGroup
1. Navigate to the EKS cluster just created.
2. Go to the Compute section under the NodeGroup Section, and click Add Node group. This allows us to create a fleet of EC2 instances all using the same configuration for the workers and set the number of workers that we need.
3. Configure the node Group add the Fleet EC2 Name, set the IAM role which was created in the previos step.
4. Select the AMI type, instance type and the EBS size.
5. Set the Node Group Scalig configuration. This create the autoscaling group which allows to scale up or scale down based on the EC2 resoources. The minimum tells the minimum instaces running, the maximum is the maxmium instances allowed when the EC2 resources are max out and we need more instanes, and the desirez size indictaes, the optimium scenario, regular use case we want to have for EC@ instances.
6. Select the network configuration used for the VPC.
7. Allow remote access to nodes, select and EC2 key pair or create a new one if needed.
8. Allow remote access in this case from all, but you can define security groups to restrict access.
9. From CLI verify that the worker nodes have been created.
    ```bash
    kubectl get nodes
    ```

### Creating a Idntity Provider to create a role with a Custom Policy
This role allows communication between the third-party service, in this case, kubectl, and AWS. So we are tellling AWS to trust our
1. Navigate to IAM service and go to Policies.
2. Create a new policy and selec JSON.
3. Copy and paste the policy.
4. Go to IAM under the  Identity provider add a provider.
5. Select the provider details: OpenID Connect, and set the privder URL. The provider URL is found in the EKS cluster in the overview section, under OpenID Connect Provider URL
6. Set the audience to sts.amazonaws.com
7. Create.
8. Go to IAM, under the section roles and create a new role.
9. Select the Web Identity, this allow the thir dparty srvice to assume this role and have access to our cluster.
10. Select the Identity Provider just created.
11. Set the audience.

### AutoScaling Group and AutoSclaer YAML file.
1. Go to the EC2 menu and under the AutoScaling Goup select the Autosclaing group created.
2. The tag section indicates the tags used in the cluster.
3. Get the deployment information for the 







