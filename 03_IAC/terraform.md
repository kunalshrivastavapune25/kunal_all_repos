https://docs.google.com/document/d/e/2PACX-1vRw6zcMfrLtyZAeGqmPofQUM2XmsHS2w0BG84WDO5fCZD_AU9C77V-GkYAHMJ2o-Hr3qkm2DKkpfABN/pub


### 66. `for_each` vs. `count` (or `for` loops)

In Terraform, `for_each` and `count` are **meta-arguments** used to create multiple instances of a resource, while `for` is a **functional expression** used to transform lists or maps.

* **`for_each`**: Best for creating resources based on a **map** or a **set of strings**. It’s more robust because if you remove an item from the middle of the map, Terraform only deletes that specific resource.
* **`for`**: Used inside a block to loop over a collection and produce a new one (e.g., creating a list of IDs from a list of objects).
* **`count`**: Best for simple scaling (e.g., "give me 3 of these"). However, removing index 1 in a list of 3 often causes Terraform to "shift" and recreate subsequent resources.
<img width="1050" height="514" alt="image" src="https://github.com/user-attachments/assets/2cda4ddc-5c10-4043-a567-a9263db572c1" />

---

### 67. Modules: The "Why" and "What"

**Modules** are containers for multiple resources that are used together. Think of them as "functions" for your infrastructure.

* **Why use them?**
* **Reusability:** Write once (e.g., a standard VPC setup) and use it across Prod, Dev, and Staging.
* **Organization:** Keeps code clean and readable.
* **Encapsulation:** Hide complex logic and only expose necessary variables.



---

### 68–71. The Terraform Statefile

The statefile (`terraform.tfstate`) is the **source of truth**. It maps your code to real-world resources and tracks metadata (like IDs and dependencies) that aren't in your `.tf` files.

* **69. Storing in Git?** **Never do this.** Git is for code, not state. Statefiles often contain secrets (passwords, keys) in plain text, and Git does not support **State Locking**, which leads to corruption.
* **70. State Management:** This involves using a **Backend** (like S3, GCS, or Terraform Cloud). Proper management includes versioning the state bucket and ensuring encryption at rest.
* **71. Concurrent Updates:** If two engineers run `terraform apply` at once, the **State Lock** kicks in. The second person will receive an error message stating that the state is locked by another process. This prevents "race conditions" where one person's changes overwrite the other's.

---

### 72. Storing State Without Cloud

If you don't have a cloud provider, you can store it:

* **Locally:** Using the default `local` backend (risky for teams).
* **Self-hosted Backend:** Using **HashiCorp Vault**, **GitLab managed state**, or a **Postgres database**.
* **Artifactory:** Many enterprises use JFrog Artifactory to host state files.

---

### 73. Enterprise vs. Community

* **Community (CLI):** Free, open-source, and manual. You handle the backend, locking, and CI/CD yourself.
* **Enterprise/Cloud:** Provides a GUI, Role-Based Access Control (RBAC), private module registries, and "Policy as Code" (Sentinel).

---

### 74. OpenTofu vs. Terraform

**OpenTofu** is an open-source fork of Terraform (created after HashiCorp changed Terraform’s license to BSL).

* **Is it better?** "Better" is subjective. OpenTofu is committed to being truly open-source (Linux Foundation) and has introduced features like improved provider functions. However, Terraform still has the direct backing of HashiCorp and a massive ecosystem. Most users choose based on licensing requirements.

---

### 75. Code Example: AWS S3 Bucket

Here is a simple snippet to create an S3 bucket:

```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-unique-devops-bucket-2026"

  tags = {
    Name        = "DevOps Project"
    Environment = "Dev"
  }
}

```

---

### 76. Resource vs. Data Source

* **Resource:** You want to **create/manage** something. Terraform is responsible for its lifecycle (Create, Update, Delete).
* *Example:* `resource "aws_instance" "web" { ... }`


* **Data Source:** You want to **fetch information** about something that already exists (either manually created or managed by a different Terraform project). It is "read-only."
* *Example:* `data "aws_ami" "ubuntu" { ... }` (fetching the latest Ubuntu ID to use in a resource).
https://github.com/AmanPathak-DevOps/End-to-End-Kubernetes-DevSecOps-Tetris-Project/blob/master/README.md

https://blog.stackademic.com/devsecops-mastery-a-step-by-step-guide-to-deploying-tetris-on-aws-eks-with-jenkins-and-argocd-3adcf21b3120

<img width="326" height="507" alt="image" src="https://github.com/user-attachments/assets/c49fb63a-6b83-4d4e-87a6-e1074359dd5c" />

terraform init
terraform plan
terraform apply


## 1. How to make eks tf


<img width="321" height="552" alt="image" src="https://github.com/user-attachments/assets/690a3bde-2d9b-4cba-bc89-938da3f2879d" />
backend

```hcl

terraform {
  backend "s3" {
    bucket       = "dev-aman-tf-bucket"
    region       = "us-east-1"
    key          = "End-to-End-Kubernetes-DevSecOps-Tetris-Project/EKS-TF/terraform.tfstate"
    use_lockfile = true
    encrypt      = true
  }
  required_version = ">=1.14.0"
  required_providers {
    aws = {
      version = ">= 5.49.0"
      source  = "hashicorp/aws"
    }
  }
}
```
provider

```hcl
provider "aws" {
  region = "us-east-1"
}
```

```hcl
variable
vpc-name            = "Jenkins-vpc"
igw-name            = "Jenkins-igw"
subnet-name         = "Jenkins-subnet"
subnet-name2        = "Jenkins-subnet2"
security-group-name = "Jenkins-sg"
rt-name2            = "Jenkins-route-table2"
iam-role-eks        = "Tetris-iam-role-eks"
iam-role-node       = "Tetris-iam-role-ec2"
iam-policy-eks      = "Tetris-iam-policy-eks"
iam-policy-node     = "Tetris-iam-policy-node"
cluster-name        = "Tetris-EKS-Cluster"
eksnode-group-name  = "Tetris-Node-Group"
```

eks

```hcl
resource "aws_eks_cluster" "eks-cluster" {
  name     = var.cluster-name
  role_arn = aws_iam_role.EKSClusterRole.arn
  vpc_config {
    subnet_ids         = [data.aws_subnet.subnet.id, aws_subnet.public-subnet2.id]
    security_group_ids = [data.aws_security_group.sg-default.id]
  }

  version = 1.33

  depends_on = [aws_iam_role_policy_attachment.AmazonEKSClusterPolicy]
}

data "aws_vpc" "vpc" {
  filter {
    name   = "tag:Name"
    values = [var.vpc-name]
  }
}

data "aws_internet_gateway" "igw" {
  filter {
    name   = "tag:Name"
    values = [var.igw-name]
  }
}

data "aws_subnet" "subnet" {
  filter {
    name   = "tag:Name"
    values = [var.subnet-name]
  }
}

```
## 2. Jenkins-Server-TF

## 3. Jenkins-Pipeline-Code
<img width="268" height="169" alt="image" src="https://github.com/user-attachments/assets/62d277b2-166a-4158-beba-ee8a701fc50a" />

```hcl
properties([
    parameters([
        string(defaultValue: 'variables.tfvars', description: 'Specify the file name', name: 'File-Name'),
        choice(choices: ['plan', 'apply', 'destroy'], description: 'Select Terraform action', name: 'Terraform-Action')
    ])
])

pipeline {
    agent any
    stages {
        stage('Checkout from Git') {
            steps {
                git branch: 'master', url: 'https://github.com/AmanPathak-DevOps/End-to-End-Kubernetes-DevSecOps-Tetris-Project.git'
            }
        }
        stage('Initializing Terraform') {
            steps {
                withAWS(credentials: 'aws-key', region: 'us-east-1') {
                dir('EKS-TF') {
                    script {
                        sh 'terraform init'
                    }
                }
                }
            }
        }
        stage('Validate Terraform Code') {
            steps {
                withAWS(credentials: 'aws-key', region: 'us-east-1') {
                dir('EKS-TF') {
                    script {
                        sh 'terraform validate'
                    }
                }
                }
            }
        }
        stage('Terraform Plan') {
            steps {
                withAWS(credentials: 'aws-key', region: 'us-east-1') {
                dir('EKS-TF') {
                    script {
                        sh "terraform plan -var-file=${params.'File-Name'}"
                    }
                }
                }
            }
        }
        stage('Terraform Action') {
            steps {
                withAWS(credentials: 'aws-key', region: 'us-east-1') {
                script {
                    echo "${params.'Terraform-Action'}"
                    dir('EKS-TF') {
                        script {
                            if (params.'Terraform-Action' == 'plan') {
                                sh "terraform plan -var-file=${params.'File-Name'}"
                            } else if (params.'Terraform-Action' == 'apply') {
                                sh "terraform apply -auto-approve -var-file=${params.'File-Name'}"
                            } else if (params.'Terraform-Action' == 'destroy') {
                                sh "terraform destroy -auto-approve -var-file=${params.'File-Name'}"
                            } else {
                                error "Invalid value for Terraform-Action: ${params.'Terraform-Action'}"
                            }
                        }
                    }
                }
                }
            }
        }
    }
}



pipeline {
    agent any
    tools {
        nodejs 'nodejs'
    }
    environment  {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('Cleaning Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'master', url: 'https://github.com/AmanPathak-DevOps/End-to-End-Kubernetes-DevSecOps-Tetris-Project.git'
            }
        }
        stage('Sonarqube Analysis') {
            steps {
                dir('Tetris-V1') {
                    withSonarQubeEnv('sonar-scanner') {
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Tetris \
                        -Dsonar.projectKey=Tetris '''
                    }
                }
            }
        }
        stage('Quality Check') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('Installing Dependencies') {
            steps {
                dir('Tetris-V1') {
                    sh 'npm install'
                }
            }
        }
        stage('OWASP Dependency-Check Scan') {
            steps {
                dir('Tetris-V1') {
                    dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }
        stage('Trivy File Scan') {
            steps {
                dir('Tetris-V1') {
                    sh 'trivy fs . > trivyfs.txt'
                }
            }
        }
        stage("Docker Image Build") {
            steps {
                script {
                    dir('Tetris-V1') {
                        withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                            sh 'docker system prune -f'
                            sh 'docker build -t tetrisv1 .'
                        }
                    }
                }
            }
        }
        stage("Docker Image Pushing") {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh 'docker tag tetrisv1 avian19/tetrisv1:${BUILD_NUMBER}'
                        sh 'docker push avian19/tetrisv1:${BUILD_NUMBER}'
                    }
                }
            }
        }
        stage("TRIVY Image Scan") {
            steps {
                sh 'trivy image avian19/tetrisv1:${BUILD_NUMBER} > trivyimage.txt'
            }
        }
        stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/AmanPathak-DevOps/End-to-End-Kubernetes-DevSecOps-Tetris-Project.git'
            }
        }
        stage('Update Deployment file') {
            environment {
                GIT_REPO_NAME = "End-to-End-Kubernetes-DevSecOps-Tetris-Project"
                GIT_USER_NAME = "AmanPathak-DevOps"
            }
            steps {
                dir('Manifest-file') {
                    withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                        sh '''
                            git config user.email "aman07pathak@gmail.com"
                            git config user.name "AmanPathak-DevOps"
                            BUILD_NUMBER=${BUILD_NUMBER}
                            echo $BUILD_NUMBER
                            imageTag=$(grep -oP '(?<=tetrisv1:)[^ ]+' deployment-service.yml)
                            echo $imageTag
                            sed -i "s/tetrisv1:${imageTag}/tetrisv1:${BUILD_NUMBER}/" deployment-service.yml
                            git add deployment-service.yml
                            git commit -m "Update deployment Image to version \${BUILD_NUMBER}"
                            git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:master
                        '''
                    }
                }
            }
        }
    }
}

```

## 4. How to make jenkins sercer
<img width="444" height="655" alt="image" src="https://github.com/user-attachments/assets/46ec2b6f-6e3f-4fef-8ec2-255980f9b31b" />



## 5. How to make eks tf


# 🚀 End-to-End DevSecOps Kubernetes Project 🌐

[![LinkedIn](https://img.shields.io/badge/Connect%20with%20me%20on-LinkedIn-blue.svg)](https://www.linkedin.com/in/aman-devops/)
[![GitHub](https://img.shields.io/github/stars/AmanPathak-DevOps.svg?style=social)](https://github.com/AmanPathak-DevOps)
![DevSecOps](https://img.shields.io/badge/DevSecOps-Mastery-brightgreen)
![Kubernetes](https://img.shields.io/badge/Kubernetes-Orchestration-blueviolet)
![Jenkins](https://img.shields.io/badge/Jenkins-Automation-orange)
![ArgoCD](https://img.shields.io/badge/ArgoCD-Continuous%20Delivery-blue)
![Docker](https://img.shields.io/badge/Docker-Containerization-blue)
![Terraform](https://img.shields.io/badge/Terraform-Infrastructure%20as%20Code-9cf)


![Infrastructure Diagram](assets/Infra.gif)

Welcome to an immersive DevSecOps learning experience! This project guides you through deploying a Tetris game on AWS EKS while mastering the art of DevSecOps.

## Directories 📂

1. **EKS-TF:** Explore Terraform scripts for deploying EKS clusters on AWS.
2. **Jenkins-Pipeline-Code:** Jenkins pipeline code for automated CI/CD.
3. **Jenkins-Server-TF:** Terraform scripts for provisioning Jenkins servers on AWS EC2.
4. **Manifest-file:** Kubernetes manifest files for Tetris application deployment.
5. **Tetris-V1:** Initial version of the Tetris game application.
6. **Tetris-V2:** Enhanced version of the Tetris game application.

## Getting Started 🚀

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/AmanPathak-DevOps/End-to-End-Kubernetes-DevSecOps-Tetris-Project.git
2. **Explore the Directories:**
   Navigate into each directory to find detailed scripts, pipelines, and configurations.

3. **Follow the Blog:**
   Implementation details and insights are documented in the associated [blog post](https://amanpathakdevops.medium.com/devsecops-mastery-a-step-by-step-guide-to-deploying-tetris-on-aws-eks-with-jenkins-and-argocd-3adcf21b3120).

## Tools Explored 🛠️
1. **Jenkins:** Automated CI/CD pipelines
2. **ArgoCD:** Continuous deployment to Kubernetes
3. **Kubernetes:** Orchestration for containerized applications
4. **Trivy:** Container vulnerability scanner
5. **OWASP Dependency-Check:** Ensuring secure dependencies
6. **Docker:** Containerized application deployment
7. **SonarQube:** Unveiling code quality insights
8. **Terraform:** Infrastructure as Code for AWS EKS

## Blog Implementation 📝
   To implement this project, follow the step-by-step guide in our detailed [blog post](https://amanpathakdevops.medium.com/devsecops-mastery-a-step-by-step-guide-to-deploying-tetris-on-aws-eks-with-jenkins-and-argocd-3adcf21b3120). 




