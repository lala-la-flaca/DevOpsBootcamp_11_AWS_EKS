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
### Creating an IM Role for the EKS Cluster
1. Open the AWS console.
2. In the search bar, type IAM, and select Roles.
3. Select Create Role.
4. Under Trusted entity type, choose AWS service.
5. For use case, select Elastic Kubernetes Service (EKS).
6. Follow the prompts to complete the configuration.
7. Select Create Role to finish.



### Using CloudFormation Template (IaC) to Create the VPC
1. Search for the CloudFormation.
2. Select Create stack and With new resources (standard).
3. Under Specify template, choose Amazon S3 URL and enter the template URL from the [AWS EKS userguide](https://docs.aws.amazon.com/eks/latest/userguide/creating-a-vpc.html)
5. Select Next.
6. Enter a Stack name and specify the required network configuration. For this example, use the default subnets provided by AWS.
7. Select Next, review the settings, and choose Create stack.
8. The CloudFormation template creates 22 resources to configure the VPC and its dependencies.

### Creating EKS Cluster for Control Plane Nodes
1. Navigate to the EKS service.
2. Select Create EKS Cluster.
3. In the configuration section:
     * Choose custom configuration.
     * Enter a name for the cluster.
     * Select the IAM role previously created.
5. In the Cluster access section:
     * Enable Cluster Administration access.
     * Set the authentication mode to EKS API and ConfirMap.
7. In the Networking section:
     * Choose the VPC, subnets, and security group created with the CloudFormation template.
9. In the cluster endpoint access section:
    * Select Public and private access: This option allows you to access the endpoint from outside  the VPC while also enabling private access within the VPC resources.

### Getting the config file to connect our kubctl with the EKS Cluster.
1. In the terminal, run the following command to list the AWS configuration details.
2. Create the local kubeconfig file to enable access to your EKS cluster:
   ```bash
   aws eks update-kubeconfig eks-cluster-test
   ```
3. Verify the kubeconfig file was created successfully.
   ```bash
   cat ~/.kube/config
   ```
5. List the namespaces in the cluster
   ```bash
   kubectl get namespaces
   ```
7. Confirm that kubectl is correctly connected to EKS by retrieving cluster information.
   ```bash
   kubectl cluster-info
   ```

### Creating Role for Worker Nodes (kubelet)
1. In the AWS console, open the I AM service and select Roles.
2. Click Create Role.
3. For the trusted entity type, select AW service.
4. For the use case, select EC2. This role allows worker node EC2 instances to communicate with AWS services and the EKS cluster.
5. Attach the following policies to grant necessary permissions:
   * AmazonEKSWorkerNodePolicy
   * AmazonEC2ContainerRegistryReadOnly
   * AmazonEKS_CNI_Policy
6. Click Next, review the configuration, name the role (EKSWorkerNodeRole), and create it.


### Creating NodeGroup
1. In the AWS Console, open the EKS service and select the cluster you created.
2. Under the Compute section, click Add Node Group.
 This step creates a fleet of EC2 instances (worker nodes) with a shared configuration and a specified number of nodes
3. Configure the node Group:
    * Enter a name for the EC2 Fleet.
    * Select the IAM role created in the previous step.
4. Choose the following settings:
   * Select the AMI type.
   * Instance type.
   * the EBS volume size.
6. Configure Scaling Settings:
   * Minimum size: the lowest number of instances that must run.
   * Maximum size: the upper limit when scaling out due to demand.
   * Desired size: the number of instances to maintain under normal conditions.
8. Select the network configuration for the VPC.
9. Allow remote access to the nodes.  Select an existing EC2 key pair or create a new one if needed.
10. Define remote access settings. Allow remote access from all IP addresses or restrict access using security groups.
11. Verify that the worker nodes have been created.
    ```bash
    kubectl get nodes
    ```

### Creating an Identity Provider an Role with a Custom Policy
This process enables communication between a third-party service (such as kubectl) and AWS by establishing trust through an identity provider.

#### Create a Custom IAM Policy
1. Navigate to the IAM service, go to Policies.
2. Click Create policy and select the JSON tab.
3. Paste your custom policy into the editor.
4. Click Next, review the policy, and create it.

#### Add an OpenID Connect (OIDC) Identity Provider
1. In the IAM service, go to Identity providers and click Add provider.
2. For Provider type, select OpenID Connect (OIDC).
3. In the Provider URL field, enter the OpenID Connect URL. You can find this URL in your EKS cluster, under the Overview section.
4. Set the Audience to sts.amazonaws.com.
5. Click Add provider.

#### Create an IAM Role for Web Identity
1. In the IAM service, go to Roles and click Create role.
2. For Trusted entity type, select Web identity.
3. Choose the Identity provider you just created.
4. Set the Audience to sts.amazonaws.com.
5. Proceed to attach the custom policy you created earlier.
6. Review and create the role.


### AutoScaling Group and AutoScaler YAML file.
1. Go to the EC2 menu and under the AutoScaling Group select the AutoScaling group created.
2. The tag section indicates the tags used in the cluster.
3. Get the sutoscaler-yaml file.
4. Open the file and modify the ServiceAccount information to set the EKSServiceAccountRole
5. Modify the deployment section to add the cluster-autoscaler.kubernetes.io/safe-to-evict: "false"
6. Modify the deployment section with the cluster name and add the following options.
7. Modify the container image used and its tag to match the Kubernetes version used by the cluster and the latest tag.
9. Apply the autoscaler yaml file.
10. Verify the running pods in the namespace

### Deploying the Nginx app
1. Get the Deployment yaml file.
2. Apply the deployment file.
3. Edit the deployment file to increase  and decrease the number of replicas, and see how the loadbalancer scales up and down according to the resources needed.
   







