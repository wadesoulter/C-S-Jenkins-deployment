# Socks Shop Microservices Application Deployment Documentation

## Project Overview

### Objective

Deploy the Socks Shop microservices-based application using Infrastructure as Code (IaaC) on Kubernetes, emphasizing automation, efficiency, and security.

### Resources

- Socks Shop Microservices Demo: [GitHub Repository](https://github.com/microservices-demo/microservices-demo.github.io)
- Detailed Implementation Guide: [GitHub Repository](https://github.com/microservices-demo/microservices-demo/tree/master)

## Task Instructions

### 1. Use Infrastructure as Code

Automate the deployment process to enable rapid and reliable deployment on Kubernetes. Employ either Ansible or Terraform for managing configurations.

### 2. Focus on Clarity and Maintenance

Ensure deployment scripts and configurations are easy to understand and maintain. Prioritize readability for future updates and replication.

### 3. Key Evaluation Criteria

#### Deployment Pipeline

Design a seamless deployment pipeline that moves the application from code to a running environment on Kubernetes.

#### Monitoring and Alerts

Implement Prometheus for monitoring and configure Alertmanager for timely alerts. Monitor key metrics to ensure optimal performance.

#### Logging

Ensure comprehensive logging to track and analyze the application's operations. Use tools like Fluentd or Loki for efficient log management.

#### Tools for Setup

Select an Infrastructure as a Service (IaaS) provider for the Kubernetes cluster. Utilize either Ansible or Terraform for managing configurations.

### 4. Security and HTTPS

#### HTTPS Requirement

Ensure secure access over HTTPS by integrating Let’s Encrypt for SSL certificates. Enable secure communication between users and the application.

#### Infrastructure Security

Enhance security by implementing network perimeter security rules. Utilize Kubernetes Network Policies to control traffic between microservices.

#### Sensitive Information

Use Ansible Vault to encrypt sensitive data, providing an extra layer of security for credentials and other confidential information.

## Extra Project Requirements for Bonus Points

- **HTTPS Requirement:**
  - Implement and enforce secure access over HTTPS for the Socks Shop application.

- **Infrastructure Security:**
  - Enhance security by setting up network perimeter security rules, leveraging Kubernetes Network Policies.

- **Sensitive Information:**
  - Use Ansible Vault to encrypt sensitive data, ensuring secure handling of confidential information.

## Project Goals Summarized

This project focuses on deploying the Socks Shop microservices-based application using Infrastructure as Code, emphasizing automation, efficiency, and security. By creating a reproducible and maintainable deployment process, incorporating modern DevOps practices and tools, we aim to achieve a quick, reliable, and secure deployment on Kubernetes.

# STEPS

## Deployment

### STEP1

Create a T2 mediun  ubuntu instance on AWS to serve as  the local server for deployment.
Run the installer.sh script to install the necessary packages on the instance it functions as follows:
This script is designed to set up an Ubuntu server with various tools commonly used in development and infrastructure management. Let's break down each section:

1. **Update the Ubuntu Server**:
   - `sudo apt-get update -y`: Update the package lists for upgrades.
   - `sudo apt-get upgrade -y`: Upgrade the installed packages.

2. **Install unzip**:
   - `sudo apt-get install unzip`: Install the `unzip` utility, which is used for extracting zip archives.

3. **Install Terraform**:
   - The script adds HashiCorp's GPG key to verify the Terraform package.
   - It adds the HashiCorp repository to `/etc/apt/sources.list.d/hashicorp.list`.
   - Then it updates the package lists and installs Terraform using `apt-get`.

4. **Install kubectl**:
   - Downloads the latest version of `kubectl` using `curl`.
   - Checks the SHA256 hash of the downloaded binary against the provided checksum.
   - Installs `kubectl` to `/usr/local/bin` with appropriate permissions.

5. **Install AWS CLI**:
   - Downloads the AWS CLI installation zip file.
   - Unzips the AWS CLI installation files.
   - Runs the AWS CLI installation script with `sudo` to install it system-wide.

6. **Install Helm**:
   - Adds the Helm GPG key to verify Helm packages.
   - Adds the Helm repository to `/etc/apt/sources.list.d/helm-stable-debian.list`.
   - Updates package lists and installs Helm using `apt-get`.

7. **Install Jenkins**:
   - Installs necessary dependencies (`fontconfig` and `openjdk-17-jre`).
   - Adds the Jenkins GPG key to verify Jenkins packages.
   - Adds the Jenkins repository to `/etc/apt/sources.list.d/jenkins.list`.
   - Updates package lists and installs Jenkins using `apt-get`.
   - Enables Jenkins to start on system boot.
   - Starts Jenkins service.
   - Allows OpenSSH and port 8080 through the UFW firewall.

This script automates the setup process by installing essential tools like Terraform, kubectl, AWS CLI, Helm, and Jenkins, making it convenient for system administrators or developers to quickly provision a server for development or deployment purposes.

### STEP2

Open jenkins on the browser to begin build

To view Jenkins on a browser using an AWS instance as a local server, you'll need to follow these general steps:

1. **Ensure Jenkins is running on the AWS instance**:
   - Verify that Jenkins is installed and running on your AWS instance. You can check the status of the Jenkins service using:

     ```
     sudo systemctl status jenkins
     ```

   - If Jenkins is not running, start it using:

     ```
     sudo systemctl start jenkins
     ```

2. **Configure Security Groups**:
   - Ensure that your AWS security group allows inbound traffic on port 8080 (or the port you configured Jenkins to run on). By default, Jenkins runs on port 8080.
   - Log in to your AWS Management Console, navigate to the EC2 service, select your instance, and then update the inbound rules of the associated security group to allow traffic on port 8080 from your IP address or from anywhere (not recommended for production).

3. **Access Jenkins from your local browser**:
   - Open your web browser.
   - Enter the public IP address or public DNS name of your AWS instance followed by the port number Jenkins is running on. For example:

     ```
     http://<public_ip_address>:8080
     ```

     or

     ```
     http://<public_dns_name>:8080
     ```

   - If Jenkins is running properly and the security group is configured correctly, you should see the Jenkins login page.

4. **Initial Jenkins Setup**:
   - If this is your first time accessing Jenkins, you'll need to retrieve the initial admin password from the Jenkins server.
     - SSH into your AWS instance.
     - Retrieve the initial admin password by running:

       ```
       sudo cat /var/lib/jenkins/secrets/initialAdminPassword
       ```

   - Copy the password and paste it into the Jenkins web interface to proceed with the setup.

5. **Complete Jenkins Setup**:
   - Follow the instructions provided by the Jenkins web interface to complete the setup process, including installing recommended plugins and creating an admin user.

6. **Access Jenkins Dashboard**:
   - Once the setup is complete, you should be able to access the Jenkins dashboard by logging in with the admin credentials you created during the setup process.

By following these steps, you should be able to view Jenkins on your local browser using your AWS instance as the server. Remember to ensure proper security measures are in place, especially if exposing Jenkins to the internet.

### STEP3

Run a Jenkins pipeline build from a GitHub repository and cluster-Jenkinsfile

To run a Jenkins pipeline build from a GitHub repository, you can follow these general steps:

1. **Install Required Plugins**:
   - Ensure that Jenkins is installed with the necessary plugins to support pipeline builds, such as the "Pipeline" plugin and any other plugins required for your specific needs.

2. **Set up Jenkins Credentials**:
   - If your GitHub repository is private, you'll need to set up credentials in Jenkins to access the repository. You can create a "Username with password" credential or use SSH credentials if your repository uses SSH.

3. **Create a Jenkins Pipeline Job**:
   - Log in to your Jenkins dashboard.
   - Click on "New Item" to create a new job.
   - Enter a name for your job and select "Pipeline" as the job type.
   - Click "OK" to create the job.

4. **Configure the Pipeline Job**:
   - In the job configuration page, scroll down to the "Pipeline" section.
   - Choose whether to define the pipeline script directly in Jenkins or to retrieve it from a source code management (SCM) system like Git.
   - If using Git, select "Pipeline script from SCM" and choose "Git" as the SCM.
   - Enter the repository URL (e.g., `https://github.com/username/repository.git`) and select the appropriate credentials if the repository is private.
   - Specify the branch to build.
   - You can also configure additional options like script path, lightweight checkout, etc., depending on your pipeline configuration.

5. **Write Your Jenkinsfile**:
   - If you've chosen to define the pipeline script directly in Jenkins, you'll need to write a `cluster-Jenkinsfile`. This file defines your pipeline's stages, steps, and other configurations.
   - Commit this `cluster-Jenkinsfile` to your GitHub repository if you're retrieving the pipeline script from SCM.

6. **Save and Run the Job**:
   - Save your Jenkins job configuration.
   - Trigger the build manually by clicking on "Build Now" or let it run automatically based on your configured triggers.

7. **Monitor the Build**:
   - Once the build is triggered, you can monitor its progress on the Jenkins dashboard.
   - View console output, logs, and any artifacts generated by the build.

By following these steps, you'll be able to run a Jenkins pipeline build from your GitHub repository. Ensure that your pipeline script (`cluster-Jenkinsfile`) is properly configured to execute the necessary steps for your build process.

Overall, this pipeline script (`cluster-Jenkinsfile`) provides a straightforward approach for managing the lifecycle of an AWS EKS cluster using Terraform, allowing for easy creation and destruction of the cluster based on user-defined parameters.

This Jenkins pipeline script (`cluster-Jenkinsfile`) automates the provisioning and destruction of an AWS EKS (Elastic Kubernetes Service) cluster using Terraform. Here's a breakdown of its functionality:

1. **Agent and Environment Setup**:
   - The pipeline is configured to run on any available Jenkins agent (`agent any`).
   - Environment variables for AWS credentials (`AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`) and region (`AWS_DEFAULT_REGION`) are defined using Jenkins credentials.

2. **Parameters**:
   - A parameter named `ENVIRONMENT` is defined as a choice parameter with options 'create' and 'destroy'. This parameter allows the user to specify whether to create or destroy the EKS cluster.

3. **Stages**:
   - The pipeline consists of two stages: `Create an EKS Cluster` and `destroy an EKS Cluster`.

4. **Create Stage**:
   - The `Create an EKS Cluster` stage is responsible for provisioning the EKS cluster.
   - It executes Terraform commands (`terraform init` and `terraform apply -auto-approve`) within the `eks` directory, which likely contains Terraform configuration files for creating the EKS cluster.

5. **Destroy Stage**:
   - The `destroy an EKS Cluster` stage is responsible for tearing down the EKS cluster.
   - It executes Terraform commands (`terraform destroy -auto-approve`) within the `eks` directory to destroy the provisioned EKS cluster.

6. **Conditional Execution**:
   - Each stage includes a `when` block with an expression that checks the value of the `ENVIRONMENT` parameter. This ensures that stages are executed only when the specified environment action (`create` or `destroy`) is selected.

### STEP4

Run a Jenkins pipeline build from a GitHub repository and Jenkinsfile, the build functions as follows:

Sure, let's break down the script block by block:

1. **Pipeline Block**:
   - `pipeline { ... }`: This block defines the entire Jenkins pipeline.

2. **Agent Block**:
   - `agent any`: Specifies that the pipeline can run on any available Jenkins agent.

3. **Environment Block**:
   - `environment { ... }`: Defines environment variables to be used throughout the pipeline.
     - `AWS_ACCESS_KEY_ID`: AWS access key ID retrieved from Jenkins credentials.
     - `AWS_SECRET_ACCESS_KEY`: AWS secret access key retrieved from Jenkins credentials.
     - `AWS_DEFAULT_REGION`: Default AWS region set to "eu-west-2".

4. **Parameters Block**:
   - `parameters { ... }`: Defines parameters that users can select when triggering the pipeline.
     - `choice`: Defines a choice parameter named `ENVIRONMENT`.
       - Choices are 'create' and 'destroy', allowing users to choose whether to create or destroy the cluster.

5. **Stages Block**:
   - `stages { ... }`: Defines multiple stages within the pipeline.

6. **Stage Blocks**:
   - Each `stage { ... }` block represents a specific task or set of tasks within the pipeline.
   - There are several stages defined, such as creating Prometheus, deploying Sock Shop to EKS, deploying ingress rule to EKS, creating nginx-controller and route53, and destroying each of these components.

7. **When Blocks**:
   - `when { expression { ... } }`: Defines conditions under which a stage should be executed.
   - Each stage has a `when` block that checks the value of the `ENVIRONMENT` parameter.
   - If `ENVIRONMENT` is set to 'create', the respective stage will be executed for creation tasks. If it's set to 'destroy', the respective stage will be executed for destruction tasks.

8. **Steps Blocks**:
   - `steps { ... }`: Contains the actual commands or scripts to be executed within each stage.
   - Inside each stage, there's a `script` block where commands are executed.
   - The `dir` function is used to change the directory before executing Terraform commands.
   - `sh` is used to execute shell commands.
   - Terraform commands such as `terraform init` and `terraform apply -auto-approve` are used for infrastructure provisioning and destruction.

Overall, this pipeline script automates the process of creating and destroying various components in an AWS EKS environment using Terraform. It provides flexibility for users to choose the desired action (create or destroy) through the `ENVIRONMENT` parameter.

### STEP5

View built infrastructures on AWS
View secured deployed pages on your browser using a registered  domain name for example (`wadesoultaker.com.ng`) as shown in the scripts;
(`micro.tf`)
(`prome.tf`)
(`variables.tf`)

# DISCLAIMER

Most services required for this project are paid for services on AWS a good knowledge of AWS pricing per service is required.

This project and its codebase are provided as an example of how services are created.

a good knowledge of jenkins is required to recreate the this project.
