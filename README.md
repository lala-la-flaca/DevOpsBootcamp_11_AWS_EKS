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
- Ensure you have an AWS Account.
- Ensure AWS CLI is installed and configured.
- Kubectl is installed and configured to connect to the Kubernetes cluster.
  


## 📌 Objective
Set up a fully managed Kubernetes environment using **Amazon EKS** and deploy a **Node Group** of EC2 instances to serve as worker nodes.

## 🎯 Features
- Configure IAM Roles.
- Create a VPC using a CloudFormation template.
- Create EKS cluster (Control Plane Nodes).
- Create a NodeGroup for the workers node.
- Attach the NodeGroup to the EKS Cluster.
- Configure Autoscaling Group for worker nodes.
- Deploy a sample application to the EKS Cluster.


## 🏗 Project Architecture

<img src=""/>


## ⚙️ Project Configuration
### Creating an IAM Role for the EKS Cluster
1. Open the AWS console.
3. In the search bar, type IAM, and select Roles.
4. Select Create Role.
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/1%20aws%20console%20role.png" width=800 />
6. Under Trusted entity type, choose AWS service.
7. For use case, select Elastic Kubernetes Service (EKS).
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/2%20creating%20role%20aws.png" width=800 />
9. Follow the prompts to complete the configuration.
10. Select Create Role to finish.


### Using CloudFormation Template (IaC) to Create the VPC
1. Search for the CloudFormation.
2. Select Create stack and With new resources (standard).
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/3%20vpc%20th%20cloudformation.png" width=800 />
4. Under Specify template, choose Amazon S3 URL and enter the template URL from the [AWS EKS userguide](https://docs.aws.amazon.com/eks/latest/userguide/creating-a-vpc.html)
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/4%20a%20clud%20formation%20config.png" width=800 />
6. Select Next.
7. Enter a Stack name and specify the required network configuration. For this example, use the default subnets provided by AWS.
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/4%20specify%20stack%20name%20an%20ip%20addresses.png" width=800 />
9. Select Next, review the settings, and choose Create stack.
10. The CloudFormation template creates 22 resources to configure the VPC and its dependencies.
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/4%20z%20resources%20created%20using%20cloudformation.png" width=800 />

### Creating EKS Cluster for Control Plane Nodes
1. Navigate to the EKS service.
2. Select Create EKS Cluster.
3. In the configuration section:
     * Choose custom configuration.
     * Enter a name for the cluster.
     * Select the IAM role previously created.
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/5%20create%20eks%20cluster.png" width=800 />
5. In the Cluster access section:
     * Enable Cluster Administration access.
     * Set the authentication mode to EKS API and ConfigMap.
      <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/6%20eks%20cluster%20access%20conf.png" width=800 />
7. In the Networking section:
     * Choose the VPC, subnets, and security group created with the CloudFormation template.
       <img src=""https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/7%20add%20networking%20settings%20that%20were%20creating%20with%20cloudformation%20template.png width=800 />
9. In the cluster endpoint access section:
    * Select Public and private access: This option allows you to access the endpoint from outside  the VPC while also enabling private access within the VPC resources.
      <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/8%20cluster%20endpoint%20access%20public%20rivate.png" width=800 />

### Getting the config file to connect our kubctl with the EKS Cluster.
1. In the terminal, run the following command to list the AWS configuration details.
   ```bash
   aws configure list
   ```
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/9%20configuring%20kubectl%20to%20conect%20to%20eks%20by%20kubeconfig%20file.png" width=800 />
   
3. Create the local kubeconfig file to enable access to your EKS cluster:
   ```bash
   aws eks update-kubeconfig --name eks-cluster-test
   ```
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/10%20creates%20a%20kubecnfgi%20file%20locally.png" width=800 />
   
4. Verify the kubeconfig file was created successfully.
   ```bash
   cat ~/.kube/config
   ```
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/11%20viewing%20kubeconfig%20file.png" width=800 />
   
5. List the namespaces in the cluster
   ```bash
   kubectl get namespaces
   ```
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/12%20checking%20namespaces.png" width=800 />
   
7. Confirm that kubectl is correctly connected to EKS by retrieving cluster information.
   ```bash
   kubectl cluster-info
   ```
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/13%20allows%20to%20see%20cluster%20control%20plane%20info.png" width=800 />

### Creating Role for Worker Nodes (kubelet)
1. In the AWS console, open the I AM service and select Roles.
2. Click Create Role.
3. For the trusted entity type, select AWS service.
4. For the use case, select EC2. This role allows worker node EC2 instances to communicate with AWS services and the EKS cluster.
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/14%20create%20an%20ec2%20role%20for%20kubelet%20workers%20nodes.png" width=800 />
6. Attach the following policies to grant necessary permissions:
   * AmazonEKSWorkerNodePolicy
   * AmazonEC2ContainerRegistryReadOnly
   * AmazonEKS_CNI_Policy
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/15%20assign%20iam%20policy%20to%20the%20role.png" width=800 />
7. Click Next, review the configuration, name the role (EKSWorkerNodeRole), and create it.


### Creating NodeGroup
1. In the AWS Console, open the EKS service and select the cluster you created.
2. Under the Compute section, click Add Node Group.
 This step creates a fleet of EC2 instances (worker nodes) with a shared configuration and a specified number of nodes
< img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/16%20add%20nodegroup.png" width=800 />

4. Configure the node Group:
    * Enter a name for the EC2 Fleet.
    * Select the IAM role created in the previous step.
      <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/17%20create%20a%20node%20grooup%20name%20it%20and%20assign%20role.png" width=800 />
      
5. Choose the following settings:
   * Select the AMI type.
   * Instance type.
   * the EBS volume size.
     <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/18%20configuring%20ec2%20instances.png" width=800 />
6. Configure Scaling Settings:
   * Minimum size: the lowest number of instances that must run.
   * Maximum size: the upper limit when scaling out due to demand.
   * Desired size: the number of instances to maintain under normal conditions.
8. Select the network configuration by selecting the subnets i the VPC
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/19%20allow%20remote%20access%20to%20nodes.png" width=800 />
10. Allow remote access to the nodes.  Select an existing EC2 key pair or create a new one if needed.
11. Define remote access settings. Allow remote access from all IP addresses or restrict access using security groups.
12. Verify that the worker nodes have been created.
    ```bash
    kubectl get nodes
    ```
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/21%20k8%20cluster%20created%20with%20workers%20now%20we%20can%20deploy%20continers.png" width=800 />
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/20%20instances%20running.png" width=800 />

### Creating an Identity Provider and Role with a Custom Policy
This process enables communication between a third-party service (such as kubectl) and AWS by establishing trust through an identity provider.

#### 1. Create a Custom IAM Policy
1. Navigate to the IAM service, go to Policies.
2. Click Create policy and select the JSON tab.
3. Paste your custom policy into the editor.
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/22%20create%20i%20am%20role%20with%20a%20custom%20policy.png" width=800 />
5. Click Next, review the policy, and create it.

#### 2. Add an OpenID Connect (OIDC) Identity Provider
1. In the IAM service, go to Identity providers and click Add provider.
2. For Provider type, select OpenID Connect.
3. In the Provider URL field, enter the OpenID Connect URL. You can find this URL in your EKS cluster, under the Overview section.
5. Set the Audience to sts.amazonaws.com
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/23%20create%20a%20iam%20identity%20provider%20with%20the%20openid%20url.png" width=800 />
7. Click Add provider.

#### 3. Create an IAM Role for Web Identity
1. In the IAM service, go to Roles and click Create role.
2. For Trusted entity type, select Web identity.
3. Choose the Identity provider you just created.
4. Set the Audience to sts.amazonaws.com.
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/23%20create%20role%20with%20the%20custom%20policty%20using%20web%20identity%20role.png" width=800 />
6. Proceed to attach the custom policy you created earlier.
7. Review and create the role.


### AutoScaling Group and AutoScaler YAML file.
1. In the AWS Management Console, go to the EC2 service.
2. Under Auto Scaling Groups, select the Auto Scaling group created for the EKS cluster.
3. In the Tags section, review the tags used by the cluster.
   <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/24%20tags%20form%20the%20auto%20scaling%20group%20used%20by%20the%20auto%20scaler%20k8.png" width=800 />
5. Download the cluster-autoscaler-autodiscover.yaml file
6. Open the YAML file and do the following:
   * Update the ServiceAccount section to use the EKSServiceAccountRole.
     <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/25%20cluster%20autosclaer-auto%20dicover%20yaml%20file%20updating%20serviceacocunt.png" width=800 />
   * In the Deployment metadata section, add the following annotation to prevent eviction:
     <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/26%20%20deployent%20section%20adding%20clustr%20autoscaler%20evict%20to%20false.png" width=800 />
   * In the container command section, add your cluster name:
     <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/27%20adding%20cluster%20name%20nd%20balance%20and%20skip%20nodes%20with%20system%20pods%20false.png" width=800 />
   * Update the container image and tag to match your cluster's Kubernetes version.
     <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/28%20updating%20the%20k8%20version%20in%20the%20file%20iwth%20the%20tag%20which%20is%20found%20inthe%20%20github%20repot.png" width=800 />
10. Apply the modified YAML file using kubectl:
    ```bash
    kubectl apply -f cluster-autoscaler.yaml file -n kube-system
    ```
12. Verify that the Cluster Autoscaler pod is running in the correct namespace:
    ```bash
    kubectl get pod -n kube-system
    ```
    <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/29%20get%20pods.png" width=800 />

### Deploying the Nginx app
1. Download or create the NGINX deployment YAML file.
2. Apply the deployment file using kubectl:
   ```bash
   kubectl apply -f .yaml
   ```
4. Edit the deployment file to update the number of replicas:
    * Increase the number of replicas to simulate scaling out.
    * Decrease the number of replicas to simulate scaling in.
      ```bash
      kubectl edit deployment nginx
      ```
      <img src="https://github.com/lala-la-flaca/DevOpsBootcamp_11_AWS_EKS/blob/main/Img/30%20increasing%20the%20number%20of%20deployment%20eplicas.png" width=800 />
      
5. Observe how the LoadBalancer responds to changes in resource demand by automatically adjusting the number of running pods.
  







