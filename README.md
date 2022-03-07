# Udacity

> Cloud DevOps Engineer Nanodegree, Completed May 14, 2020

Capstone Project: Jenkins Pipelines on AWS

Summary:

This project is the capstone for Udacity Cloud DevOps Nanodegree program which comprise of all that I learned through the curriculum. In this capstone project, I applied my skills and knowledge that I've gained through the Cloud DevOps Nanodegree program. This project also continued from the other projects that I completed, and so this is an enchancement.

Required:

1. AWS CLI v2
2. Docker
3. Java 11 (Jenkins)
3. Jenkins installed in Amazon EC2
4. eksctl (deploy a kubernetes cluster)
5. tidy (html linter)

Steps Taken:

> To get started, it may be best to create a Kubernetes cluster to connect to AWS EKS in the future.

1. Create a group called `jenkins` to add the IAM user `jenkins`.
    - Permissions
        - AmazonEC2FullAccess
        - AmazonS3FullAccess
        - AmazonVPCFullAccess
    - Create an IAM user called 'jenkins' to be granted access to AWS EC2 instance
2. Log into the AWS Console with the new user `jenkins`
    - Create a ssh key pair (chmod 400 *.pem)
    - Deploy an AWS EC2 Instance (Ubuntu)
        - Create a Security Group
            - Make sure TCP Port 8080 and TCP Port 22 is open
3. Install and configure Jenkins on AWS EC2 Instance
    - Launch the Jenkins website at `http://<IP>:8080` and follow the instructions to obtain the password.
        - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
        - Select `Install Suggested Plugins` to quickly finish the configuration.
        - Create first admin user `jenkins-admin:sTGkyFt4JW6Cud5`
    - Install the required plugins
        - Head to **Manage Jenkins** -> **Manage Plugins** -> Search under **Available** -> Blue Ocean -> Install without restarts
        - Head back to **Manage Plugins** and search under **Available** for Pipeline: AWS Steps and Download Now and Install after restart
4. Create a GitHub repository to store the code (skip if already created)
    - Create `index.html` and `Jenkinsfile`
    - Commit the code and push to the Git repository.
    - Copy and paste the Git url and authenticate with login.
5. Apply the AWS credentials in Jenkins
    - Select gear icon besides the project name, this will take you back to the original Jenkins website
    - Select **Credential** on the left side, and select **global** and select from the drop-down menu `AWS Credentials` (make sure that Jenkins has been restarted for this option to show up.)
    - Also add credentials for the docker account as well.
6. Create a Kubernetes cluster to be deployed to AWS.

```bash
eksctl create cluster --node-ami-family=Ubuntu2004
2022-03-06 20:09:49 [ℹ]  eksctl version 0.86.0
2022-03-06 20:09:49 [ℹ]  using region us-east-1
2022-03-06 20:09:50 [ℹ]  setting availability zones to [us-east-1c us-east-1d]
2022-03-06 20:09:50 [ℹ]  subnets for us-east-1c - public:192.168.0.0/19 private:192.168.64.0/19
2022-03-06 20:09:50 [ℹ]  subnets for us-east-1d - public:192.168.32.0/19 private:192.168.96.0/19
2022-03-06 20:09:50 [ℹ]  nodegroup "ng-ac8c49bc" will use "ami-0b7e9322d7f1443ea" [Ubuntu2004/1.21]
2022-03-06 20:09:50 [ℹ]  using Kubernetes version 1.21
2022-03-06 20:09:50 [ℹ]  creating EKS cluster "hilarious-monster-1646618989" in "us-east-1" region with managed nodes
2022-03-06 20:09:50 [ℹ]  will create 2 separate CloudFormation stacks for cluster itself and the initial managed nodegroup
2022-03-06 20:09:50 [ℹ]  if you encounter any issues, check CloudFormation console or try 'eksctl utils describe-stacks --region=us-east-1 --cluster=hilarious-monster-1646618989'
2022-03-06 20:09:50 [ℹ]  Kubernetes API endpoint access will use default of {publicAccess=true, privateAccess=false} for cluster "hilarious-monster-1646618989" in "us-east-1"
2022-03-06 20:09:50 [ℹ]  CloudWatch logging will not be enabled for cluster "hilarious-monster-1646618989" in "us-east-1"
2022-03-06 20:09:50 [ℹ]  you can enable it with 'eksctl utils update-cluster-logging --enable-types={SPECIFY-YOUR-LOG-TYPES-HERE (e.g. all)} --region=us-east-1 --cluster=hilarious-monster-1646618989'
2022-03-06 20:09:50 [ℹ]  
2 sequential tasks: { create cluster control plane "hilarious-monster-1646618989", 
    2 sequential sub-tasks: { 
        wait for control plane to become ready,
        create managed nodegroup "ng-ac8c49bc",
    } 
}
2022-03-06 20:09:50 [ℹ]  building cluster stack "eksctl-hilarious-monster-1646618989-cluster"
2022-03-06 20:09:51 [ℹ]  deploying stack "eksctl-hilarious-monster-1646618989-cluster"
2022-03-06 20:10:21 [ℹ]  waiting for CloudFormation stack "eksctl-hilarious-monster-1646618989-cluster"
```

6. Create an AWS S3 Bucket
    - Ensure that **Block Public Access Settings for this bucket** is unchecked
    - Agree to the acknowledgement
    - Select the bucket's name and head to **Permissions**
    - Edit **Bucket Policy**
7. Create the pipeline for AWS
8. Add another stage in the pipeline
    - Install `tidy` which is a linter to lint the example html file.