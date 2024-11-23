# Workshop Day Github Actions

## Lab-01: Setup Environment

**Estimated time to complete:** 50 minutes

1. Log in to your GitHub account.
2. Click on the "+" icon in the top right corner and choose "New repository."
3. Fill in the repository details:
  - Repository name: **mdc-workshop**
  - Description: (optional)
  - Choose visibility: Private
  - Initialize with: Add a README
  - Click "Create repository."
4. Setting Up Azure Credentials in **cloudshell**:
5. Run the Azure CLI command to create the service principal:
```bash
az ad sp create-for-rbac --name "mdcgithubactions" --role contributor --scopes /subscriptions/YOUR_SUB_ID --sdk-auth
```
**Note:** Replace **SUBSCRIPTION_ID** with your actual subscription ID.
6. Copy the JSON output containing the credentials.
```bash
{
  "clientId": "cef8a7ea-xxxxx-xxxx-xxxx-xxxxxxxxxxx",
  "clientSecret": "lu18Q~-xxxxxxx.xxxxxxxxxxxxxxxxxxxxxxx",
  "subscriptionId": "xxxxxxxxx-xxxx-xxxx-xxxxxxx-xxxxxxxxxx",
  "tenantId": "xxxxxxxxxx-xxxxxxx-xxxx-xxxx-xxxxxxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
7. **GitHub Actions** - Create Variables:
8. In your GitHub repository, go to "Settings" > "Security" > "Secrets and Variables" > "Actions".
- Click on "New repository secret."
9. Create the following secrets:
```bash
AZURE_CLIENT_ID: Copy the clientId from the JSON output.
AZURE_CLIENT_SECRET: Copy the clientSecret from the JSON output.
AZURE_TENANT_ID: Copy the tenantId from the JSON output.
AZURE_SUBSCRIPTION_ID: Copy the subscriptionId from the JSON output.
AZURE_CREDENTIALS: Paste the entire JSON output in this field.
ARM_SUBSCRIPTION_ID: Copy the subscriptionId from the JSON output.
```
10. Log in to your **AWS account**.
11. Access AWS IAM for Access Key:
12. In the AWS Management Console, go to the "IAM" service.
13. Access the user for whom you want to create access keys.
14. Go to the "Security credentials" tab.
15. Create a new access key and download the CSV file containing the access key and secret key information.
16. **GitHub Actions** - More Variables:
17. In your GitHub repository, go to "Settings" > "Security" > "Secrets and Variables" > "Actions", create the following secrets:
```bash
AWS_ACCESS_KEY_ID: Copy the AWS access key from the downloaded CSV file.
AWS_SECRET_ACCESS_KEY: Copy the AWS secret key from the downloaded CSV file.
```
---
18. Access **Azure Portal**:
19. Log in to the Azure Portal with your account.
20. Create a Resource Group:
21. From the left menu, click on "Resource groups."
- Click the "+ Add" button to create a new Resource Group.
- Choose a name for the Resource Group: "**mdc-rg**."
- Select your region: (US) East Us
- Click "Review + Create," then "Create" to create the Resource Group.
22. Create a Storage Account:
23. From the left menu, click "Create a resource."
24. Search for "Storage account" and select it.
25. Fill in the details:
- Subscription: Choose your subscription.
- Resource group: Select "mdc-rg" (or the name you chose).
- Storage account name: For example, "**mdcconfigID**." **NOTE:**The name needs to be unique change the last two letters by the initials of your name
- Location: Choose your desired region.
- Performance: Standard.
- Account kind: StorageV2.
- Replication: Locally redundant storage (LRS).
- Click "Next: Networking," "Next: Data protection," "Next: Advanced," and finally "Review + Create."
- Review the settings and click "Create" to create the Storage Account.
26. Create a Container within the Storage Account:
27. Access the Storage Account you created.
28. From the left menu, click on "Containers."
29. Click the "+ Container" button to add a new container.
- Choose a name for the container, **tfstates**
- Set the access configuration: private access.
- Click "Create" to create the container.
---
30. Access **Azure Portal**:
31. In the resource group, find and click on your Storage Account.
32. In the left sidebar, under "Security + Networking ," click on "Access keys."
33. Here, you will see two key pairs (Key1).
34. Click the "Copy" button next to **"Key1"** to copy the connection string to your clipboard.
35. In your **GitHub repository**, go to "Settings" > "Security" > "Secrets and Variables" > "Actions", create the following secrets:
36. Click on **"New repository secret."**
```bash
ARM_ACCESS_KEY = Paste the value
```
---
37. Go to the Azure portal.
38. Click on "Container registries".
39. Click on "Create container registry".
40. On the "Create container registry" page, enter the following information:
- Registry name - Enter **mdcregistryID**. # The name needs to be unique change the last two letters by the initials of your name
- Region - Select the region where you want to create your registry.
- Pricing tier - Select the pricing tier that you want to use for your registry.
- Security - Select the security options that you want to use for your registry.
- Resource group - Select **mdc-rg**.
41. Click "Create".
---
42. Update GitHub repository with ACR credentials
43. To allow the GitHub action to successfully Build and deploy the image to the Azure Container Registry, we need to add **GitHub repository secrets**.
44. Select Azure Container Registry which you created -> Access Keys tab within settings, enable Admin user.
45. Add 3 secrets as below from the access keys tab above **(On github actions)**:
46. In your **GitHub repository**, go to "Settings" > "Security" > "Secrets and Variables" > "Actions", create the following secrets:
- REGISTRY_LOGIN_SERVER – Login server
- REGISTRY_USERNAME – Username
- REGISTRY_PASSWORD – Password
47. On cloudshell create the following folder:
```bash
mkdir mdc-workshop-githubactions
```
48. Navigate to the EC2 dashboard on your AWS Console.
50. On the left-hand panel, click on "Key Pairs" under the "Network & Security" section.
51. Click the "Create Key Pair" button.
52. A dialog box should appear. Enter terraform-mdc as the name for your new key pair.
53. Select the format for the private key file you'd prefer. The .pem option is usually available.
54. Click on "Create Key Pair".
55. The .pem file will automatically download to your machine. Keep it in a safe location.
56. You'll need to extract the content of the .pem file to copy it. Open the terminal and navigate to the directory where the .pem file is located. Then, run the following command:
```bash
cat terraform-mdc.pem
```
57. This will output the content of the file. Copy all the text, including the **-----BEGIN RSA PRIVATE KEY-----** and **-----END RSA PRIVATE KEY-----** lines.
58. **Create a GitHub Secret**
59. Navigate to Your GitHub Repository: Open your web browser and go to your GitHub repository where you want to add the secret.
60. Go to Settings: Click on the Settings tab which is usually located at the end of the menu bar below the repository's name.
61. Secrets: On the left sidebar, click on Secrets under the Actions category.
62. New Repository Secret: Click on the New repository secret button.
63. Name the Secret: In the Name field, type **CERT_SSH**.
64. Paste the Key: In the Value field, paste the content of the terraform-mdc.pem file that you copied earlier.
65. Add Secret: Finally, click on the Add secret button to save the secret.
66. On AWS Cloud Shell 
67. Generate SSH Key: To generate a new SSH key, enter the following command in your terminal:
```bash
ssh-keygen -t rsa -b 4096
cat ~/.ssh/id_rsa.pub
```
68. Copy the Public Key: To copy your public key to your clipboard.
69. Go to GitHub: Open your web browser, navigate to GitHub, and log into your account.
70. Access Settings: Click on your profile picture in the top-right corner of the GitHub homepage, then click on "Settings".
71. SSH and GPG Keys: On the left sidebar, click on "SSH and GPG keys".
72. New SSH Key: Click the "New SSH key" button at the top-right.
73. Title and Key:
- In the "Title" field, give your SSH key a descriptive name. This could be the name of your local machine, for example.
- In the "Key" field, paste the SSH public key you copied earlier.
74. Add Key: Click "Add SSH key".

## Lab-02: Basic Github Actions

**Estimated time to complete:** 30 minutes

**Working Directory:**mdc-workshop-githubactions

1. Log in to your GitHub account.
2. Open a terminal and navigate to the directory where you want to clone the repository: **mdc-workshop**.
3. Run the following command to clone the repository:
```bash
cd mdc-workshop-githubactions
git clone git@github.com:YOUR_REPO/mdc-workshop.git
```
4. Navigate to the directory of the cloned repository.
5. Create a new folder and file called:
```bash
mkdir -p .github/workflows
touch mdc-workflow.yaml
```
6. Copy the workflow code to the mdc-workflow.yml file in the workflows folder.
7. Save the file.
```yaml
name: Mdc Workflow

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout do Código
        uses: actions/checkout@v2

  dev:
    needs: build  # O estágio de desenvolvimento depende do estágio de build
    runs-on: ubuntu-latest

    steps:
      - name: Fase de Desenvolvimento
        run: echo "Executando deploy de DEV"

  qa:
    needs: build  # O estágio de qualidade depende do estágio de build
    runs-on: ubuntu-latest

    steps:
      - name: Fase de Qualidade
        run: echo "Executando deploy de QA"

  hml:
    needs:
      - dev  # O estágio de homologação depende do estágio de desenvolvimento
      - qa   # e também do estágio de qualidade
    runs-on: ubuntu-latest

    steps:
      - name: Fase de Homologação
        run: echo "Executando deploy de HML"

  prd:
    needs: hml  # O estágio de produção depende do estágio de homologação
    runs-on: ubuntu-latest

    steps:
      - name: Fase de Produção
        run: echo "Executando deploy de PRD"
```
8. Run the following command to push the code to the repository:
```bash
git add .
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
git commit -m "Adding workflow"
git push origin main
```
9. With this, the workflow is ready to be executed. To execute the workflow, open the GitHub website and navigate to the repository you just cloned. On the repository page, click the "Actions" button. On the actions page, click the "Run workflow" button next to the workflow mdc-workflow.

## Lab-03: Base image docker

**Estimated time to complete:** 30 minutes

1. Use the cd command to navigate to the mdc-workshop-githubactions directory:
2. In the terminal, copy the app directory from the mdc-python-azure repository to the mdc-workshop-githubactions directory:
```bash
cd mdc-workshop-githubactions
git clone https://github.com/iesodias/mdc-python-azure
cp -r mdc-python-azure/app mdc-workshop/
cp -r mdc-python-azure/kube-manifest mdc-workshop/

```

5. Open the .github/workflows/mdc-workflow.yaml file in the mdc-workshop-githubactions directory using a text editor.
6. Erase all existing content.
7. Paste in the provided YAML code and save the changes.

```yaml
name: docker_build_push_acr
 
on:
  workflow_dispatch:
  push:
    branches:
      - main
 
jobs:
  docker_build_push_acr:
    name: 'Docker Build and Push to ACR'
    runs-on: ubuntu-latest
    environment: production
  
    defaults:
      run:
        shell: bash
  
    steps:
    - name: ⬇️ Checkout
      uses: actions/checkout@v2
  
    - name: 🔑 'Docker Login'
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - name: 🐳 Docker build
      uses: docker/build-push-action@v4
      with:
        context: ./app
        push: true
        tags:
          ${{ secrets.REGISTRY_LOGIN_SERVER }}/mdc-app:latest
```

- In the terminal, stage your changes for Git:

```bash
git add.
git commit -m "new deploy"
git push

```
8. Verify on Azure
9. Log in to the Azure Portal.
10. Navigate to your Azure Container Registry.
11. Check if the mdc-app container with the latest tag has been pushed.

**Note**: The provided workflow is triggered manually (workflow_dispatch). You will need to go to GitHub and manually trigger the workflow, unless you make further adjustments to trigger it automatically.

## Lab-04: Create Kubernetes Cluster

**Estimated time to complete:** 40 minutes

1. Log in to Azure Portal: Open your browser and navigate to Azure Portal. Log in using your Azure account.
2. Create Resource: On the left sidebar, click "+ Create a resource".
3. Choose Service: In the search field, type "Kubernetes" and select "Kubernetes Service".
4. Creation Page: Click "Create" to open the AKS creation page.
5. Project Details:
- Subscription: Choose the Azure subscription you wish to use.
- Resource Group: Enter mdc-rg to either create a new resource group with this name or select an existing one.
Instance Details:
- Cluster Name: Type **mdc-aks** as your cluster name.
- Region: Choose East US as the region.
- Kubernetes Version: Select the desired version (default is usually the latest stable).
- Node Details:
- Node Size: For the "Dev/Test" tier, you would generally pick a smaller VM size like Standard_B2s or similar.
- Node Count: You can leave this as 1 for Dev/Test purposes.
- Scale Settings:
- Choose your scale: leave the default options.
6. Identity and Security:
- You can usually leave the default.
7. In **Integration**
- Connect your cluster to an Azure Container Registry to enable seamless deployments from a private image registry. **mdcregistryID** <YOUR_REGISTRY_NAME>
7. Review + Create:
-Review your settings and, if all looks correct, click "Create".

8. First, retrieve the Kubernetes configuration credentials for your AKS cluster. (AZ CLOUD SHELL)
```bash
az login
az aks get-credentials --resource-group mdc-rg --name mdc-aks
```
9. Now, verify that your cluster is up by listing the worker nodes.
```bash
kubectl get nodes 
```
10. Execute the following command to get the resource group where your AKS nodes are located.
```bash
az aks show --resource-group mdc-rg --name mdc-aks --query nodeResourceGroup -o tsv
```
11. Replace <REPLACE-OUTPUT-RG-FROM-PREVIOUS-COMMAND> with the output from the previous command to create a static public IP.
```bash
az network public-ip create --resource-group <REPLACE-OUTPUT-RG-FROM-PREVIOUS-COMMAND> --name myAKSPublicIPForIngress --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```
**Note:**Make a note of the public IP address for the next step.
12. If you don't have Helm installed, you can install it using brew for macOS or apt-get for Linux. (Optional)
```bash
brew install helm
or 
apt-get install helm
```
13. Create a Kubernetes namespace where your ingress resources will reside.
```bash
kubectl create namespace ingress-basic
```
14. Add the official Helm repository and update.
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo update
```
16. Replace <REPLACE-OUTPUT-IP-PREVIOUS-COMMAND> with the static IP address you noted earlier.
```bash
# Use Helm to deploy an NGINX ingress controller
helm install ingress-nginx ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.service.externalTrafficPolicy=Local \
    --set controller.service.loadBalancerIP="<REPLACE-OUTPUT-IP-PREVIOUS-COMMAND>"
```
17. To verify the installation of the ingress controller, run:
```bash
kubectl get all -n ingress-basic
```
18. Finally, visit the public IP you've created for the ingress. You should see a "404 Not Found" message from Nginx, which indicates it's working.
```bash
http://<Public-IP-created-for-Ingress>
# Output should be
404 Not Found from Nginx
```

## Lab-05: Deploy your App on K8S

**Estimated time to complete:** 35 minutes

1. Open a terminal and navigate to the **mdc-workshop-githubactions** directory:
2. Check if the folders kube-manifest and app are present:
```bash
ls -la
```
3. You should see the **kube-manifest** and **app** folders listed.
4. Update the GitHub Actions Configuration File
5. Open the **01-mdc-github.yaml** file with a text editor of your choice, for example:
```bash
vi .github/workflows/01-mdc-github.yaml
```
6. Replace the content with the provided YAML. 
```yaml
name: Build and Deploy K8S App
 
on:
  workflow_dispatch:
  push:
    branches:
      - main
 
jobs:
  docker_build_push_acr:
    name: 'Docker Build and Push to ACR'
    runs-on: ubuntu-latest
    environment: production
  
    defaults:
      run:
        shell: bash
  
    steps:
    - name: ⬇️ Checkout
      uses: actions/checkout@v2
  
    - name: 🔑 'Docker Login'
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - name: 🐳 Docker build
      uses: docker/build-push-action@v4
      with:
        context: ./app
        push: true
        tags:
          ${{ secrets.REGISTRY_LOGIN_SERVER }}/mdc-app:latest

  deploy_on_Aks:
    name: '🚀 Deploy App on AKS'
    runs-on: ubuntu-latest
    needs: docker_build_push_acr

    steps:
    - name: '📥 Checkout code'
      uses: actions/checkout@v2

    - name: '🔐 Azure Login'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: '🔑 Get AKS Credentials'
      run: az aks get-credentials --resource-group mdc-rg --name mdc-aks --overwrite-existing

    - name: '🛳 Deploy to AKS'
      run: kubectl apply -f kube-manifest/deployment.yaml
```

7. Save and close the file.
8. In the deployment.yaml file,
9. Change to the name of the created registry:
```bash
    spec:
      containers:
        - name: mdc-app
          image: YOUR_REGISTRY_NAME/mdc-app:latest
```
8. Trigger the Pipeline
9. Add the changes to the local git repository:
```bash
git add .
```
10. Commit the changes:
```bash
git commit -m "deploy app"
```
11. Push the changes to the GitHub repository:
```bash
git push
```
12. This will trigger the GitHub Actions pipeline, which will run and do the following:
- Build the Docker image in the app folder.
- Push the image to Azure Container Registry.
- Fetch AKS credentials.
- Deploy the application to AKS using the kube-manifest/deployment.yaml file.

18. Finally, visit the public IP you've created for the ingress.
```bash
http://<Public-IP-created-for-Ingress>/mdc
http://<Public-IP-created-for-Ingress>/host
```
## Lab-06: Create a Instance on AWS

**Estimated time to complete:** 30 minutes

1. Open a terminal and navigate to the location where you want to create the new folder.
2. Run the following command to create a folder called terraform:
```bash
mkdir terraform
```
3. Create main.tf in the terraform Folder
4. Move into the terraform folder:
```bash
cd terraform
```
5. Open a text editor and paste the provided content for **main.tf**.
```t
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = ">= 3.7.0"
    }
  }

  backend "azurerm" {
    resource_group_name  = "mdc-rg"
    storage_account_name = "mdcconfigid" # YOUR STORAGE ACCOUNT NAME
    container_name       = "tfstates"
    key                  = "terraform-aws.tfstate"
    use_oidc             = true
  }

}

provider "aws" {
  region = "us-east-1"
}

data "aws_ami" "ubuntu" {
  most_recent = true
  owners = ["099720109477"] ## Canonical

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }

}

data "aws_ami" "centos" {
  most_recent = true
  owners = ["137112412989"] ## Canonical TESTE SE ESTA AUTOMATICO

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}

resource "aws_default_vpc" "default" {}

resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  #var.operating_system == "ubuntu" ? data.aws_ami.latest_ubuntu.id : data.aws_ami.latest_centos.id
  instance_type = "t3.micro"
  key_name      = "terraform-mdc"
  vpc_security_group_ids      = [aws_security_group.ssh.id]


  tags = {
    Name = "HelloWorld"
  }
}

resource "aws_security_group" "ssh" {
  name        = "allow_ssh_ga"
  description = "Allow ssh inbound traffic"
  vpc_id = aws_default_vpc.default.id

  ingress {
    description      = "WebServer from VPC"
    from_port        = 80
    to_port          = 80
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  ingress {
    description      = "SSH from VPC"
    from_port        = 22
    to_port          = 22
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  egress {
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  tags = {
    Name = "allow_ssh"
  }
}
```
6. Save the file in the terraform folder as main.tf.
7. Update GitHub Actions Workflow File
8. Navigate to the GitHub repository where you want to add this workflow.
9. Go to the .github/workflows folder and open the existing workflow file.
10. Erase the existing content and paste the new YAML content provided.
```yaml
name: Create AWS Instance

env:
  AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
  AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
  ARM_ACCESS_KEY: ${{ secrets.ARM_ACCESS_KEY }}
  RUN_TERRAFORM_DESTROY: false

on:
  workflow_dispatch:
    inputs:
      destroyInfra:
        description: 'Destroy Infrastructure?'
        required: false
        default: 'false'

jobs:
  Terraform_Plan:
    name: TerraformPlanValidate
    runs-on: ubuntu-latest
    steps:
    
    - name: Checkout
      uses: actions/checkout@v3

    - name: Set up Terraform
      uses: hashicorp/setup-terraform@v1
      with:
        terraform_version: latest

    - name: Login via Az module
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}}
        enable-AzPSSession: true 

    - name: Terraform Init
      run: |
        terraform -chdir=terraform/ init

    - name: Terraform Validate
      run: terraform -chdir=terraform/ validate -no-color

    - name: Terraform Plan
      run: terraform -chdir=terraform/ plan -no-color -input=false
      continue-on-error: true

    - name: Terraform Plan Status
      if: steps.plan.outcome == 'failure'
      run: exit 1

  Deploy_Terraform:
      name: 'Terraform Apply'
      needs: [Terraform_Plan]
      runs-on: ubuntu-latest

      steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Terraform Init
        run: |
          terraform -chdir=terraform/ init

      - name: Terraform Apply
        run: |
          terraform -chdir=terraform/ apply -auto-approve -input=false
      
  Destroy_Terraform:
    name: 'Conditional Terraform Destroy'
    needs: [Deploy_Terraform]
    runs-on: ubuntu-latest
    if: github.event.inputs.destroyInfra == 'true'
    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Terraform Init
      run: |
        terraform -chdir=terraform/ init

    - name: Terraform Destroy
      run: |
        terraform -chdir=terraform/ destroy -auto-approve -input=false
```
11. Save the file.
12. Add the changes to the local git repository:
```bash
git add .
```
13. Commit the changes:
```bash
git commit -m "you commit message here"
```
14. Push the changes to the GitHub repository:
```bash
git push
```
15. Workflow Explanation
- The workflow is initiated manually via a workflow_dispatch.
- A new input field called destroyInfra has been added. This field will determine whether to destroy the infrastructure or not.
- The job Destroy_Terraform will only run if the destroyInfra field is set to 'true'.
16. Now, you can run the workflow directly from GitHub. When you opt to run it, an option to input the value for destroyInfra will appear. Input 'true' if you wish to destroy the infrastructure or leave it as the default 'false'.

## Lab-07: Using Ansible to install apps

**Estimated time to complete:** 50 minutes

1. Create Folders for Ansible and Terraform
```bash
mkdir ansible
cd ansible
vi playbook.yaml
```
2. Add the provided content into playbook.yaml
```yaml
---
- hosts: all
  become: yes
  tasks:
    - name: Update OS packages
      apt:
        update_cache: yes
        upgrade: dist

    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Start Nginx service
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Install Docker dependencies
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - gnupg
          - lsb-release
        state: present

    - name: Add Docker GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    - name: Add Docker repository
      apt_repository:
        repo: "deb [arch=amd64] https://download.docker.com/linux/ubuntu {{ ansible_lsb.codename }} stable"
        state: present

    - name: Install Docker
      apt:
        name: docker-ce
        state: present

    - name: Start Docker service
      service:
        name: docker
        state: started
        enabled: yes

    - name: Add user to the docker group
      user:
        name: "ubuntu"
        groups: docker
        append: yes
```
3. Create the output.tf File
4. Inside the terraform/ directory, create the file **output.tf** and add the following content:
```r
output "instance_public_ip" {
  value = aws_instance.web.public_ip
  description = "The public IP address of the web instance."
}
```
5. GitHub Actions Workflow
6. Inside the .github/workflows/ directory of your repository, create or update a YAML file for the workflow. Paste the provided YAML for the GitHub Actions workflow.
```yaml
name: Create AWS Instance

env:
  AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
  AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
  ARM_ACCESS_KEY: ${{ secrets.ARM_ACCESS_KEY }}
  CERT_SSH: ${{ secrets.CERT_SSH }}
  ANSIBLE_HOST_KEY_CHECKING: false
  RUN_TERRAFORM_DESTROY: false

on:
  workflow_dispatch:
    inputs:
      destroyInfra:
        description: 'Destroy Infrastructure?'
        required: false
        default: 'false'

jobs:
  Terraform_Plan:
    name: TerraformPlanValidate
    runs-on: ubuntu-latest
    steps:
    
    - name: Checkout
      uses: actions/checkout@v3

    - name: Set up Terraform
      uses: hashicorp/setup-terraform@v1
      with:
        terraform_version: latest

    - name: Login via Az module
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}}
        enable-AzPSSession: true 

    - name: Terraform Init
      run: |
        terraform -chdir=terraform/ init

    - name: Terraform Validate
      run: terraform -chdir=terraform/ validate -no-color

    - name: Terraform Plan
      run: terraform -chdir=terraform/ plan -no-color -input=false
      continue-on-error: true

    - name: Terraform Plan Status
      if: steps.plan.outcome == 'failure'
      run: exit 1

  Deploy_Terraform:
      name: 'TerraformApply'
      needs: [Terraform_Plan]
      runs-on: ubuntu-latest

      steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Terraform Init
        run: |
          terraform -chdir=terraform/ init

      - name: Terraform Apply
        run: |
          terraform -chdir=terraform/ apply -auto-approve -input=false
      
      - name: Get Public IP
        id: get-ip
        run: echo "INSTANCE_IP=$(terraform -chdir=terraform/ output -raw instance_public_ip)" >> $GITHUB_ENV

      - name: Create Artifact File
        run: echo "${{ env.INSTANCE_IP }}" > artifact.txt

      - name: Create Artifact
        uses: actions/upload-artifact@v4
        with:
          name: instance-ip
          path: artifact.txt
          if-no-files-found: error

  Destroy_Terraform:
    name: 'ConditionalTerraformDestroy'
    needs: [Deploy_Terraform]
    runs-on: ubuntu-latest
    if: github.event.inputs.destroyInfra == 'true'
    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Terraform Init
      run: |
        terraform -chdir=terraform/ init

    - name: Terraform Destroy
      run: |
        terraform -chdir=terraform/ destroy -auto-approve -input=false

  Ansible_Configure:
    name: 'AnsibleInstallApps'
    needs: [Deploy_Terraform]
    if: github.event.inputs.destroyInfra == 'false'
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Download Artifact
        uses: actions/download-artifact@v4
        with:
          name: instance-ip
          
      - name: Read IP Address
        id: read-ip
        run: echo "INSTANCE_IP=$(cat artifact.txt)" >> $GITHUB_ENV

      - name: Use IP Address
        run: echo "The IP address is ${{ env.INSTANCE_IP }}"

      - name: Config SSH File
        run: |
          echo "Criando Arquivo"
          echo "$CERT_SSH" > ssh-key.pem
          chmod 600 ssh-key.pem

      - name: Create Ansible Inventory
        run: |
          echo "[webservers]" > inventory
          echo "${{ env.INSTANCE_IP }} ansible_ssh_private_key_file=ssh-key.pem" >> inventory

      - name: Ansible Playbook Install
        run: |
          ansible-playbook -i inventory -u ubuntu ansible/playbook.yaml
```
7. Add the changes to the local git repository:
```bash
git add .
```
8. Commit the changes:
```bash
git commit -m "deploy app"
```
9. Push the changes to the GitHub repository:
```bash
git push
```
10. This will trigger your GitHub Actions workflow.

## Lab-08: Creating Pull Request

**Estimated time to complete:** 40 minutes

1. Create a Personal Access Token (PAT)
2. Go to GitHub Settings -> Developer settings -> Personal access tokens -> Generate new token.
3. Select all necessary permissions and click "Generate token".
4. Copy the generated token.
5. Add PAT to Repository Secrets
6. Go to your GitHub repository -> Settings -> Secrets.
7. Click "New repository secret" and name it TOKEN_GB. Paste the generated token into the value field.
8. Open your terminal and navigate to the project folder.
```bash
cd mdc-workshop-githubactions/mdc-workshop/
rm -rf .github/workflows/*
cd .github/workflows/
touch 00-feature.yaml 01-feature-to-develop.yaml 02-develop-to-homol.yaml 03-homol-to-prod.yaml
```
09. Copy the content for each YAML file provided in your question and paste it into the corresponding file you've created.
10. **00-feature.yaml**
```yaml
name: Automate Pull Request - Feature to Develop

on:
  push:
    branches:
      - feature/*

jobs:

  createPullRequest:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Create Pull Request
      uses: repo-sync/pull-request@v2
      with:
        github_token: ${{ secrets.TOKEN_GB }}
        destination_branch: "develop"  # Merge into this branch
        pr_title: "Automated PR from ${{ github.ref }}"
        pr_body: "## 🚀 Changes Introduced\n\nDescrição das principais mudanças que este PR traz.\n\n## 🖼️ Screenshots\n\n## ⚙️ Testes\n\n## 📦 Dependências\n\n##  Miscelânea \n\n## ☑️ Checklist\n\n- [ ] Código testado\n- [ ] Documentação atualizada\n- [ ] Reviewers alocados\n\n## 🎉 Como testar"
        pr_draft: false
```
11. **01-feature-to-develop.yaml**
```yaml
name: Automate Pull Request - Feature to Develop

on:
  push:
    branches:
      - develop

jobs:

  docker_build_push_acr:
    name: 'Docker Build and Push to ACR'
    runs-on: ubuntu-latest
    environment: production
  
    defaults:
      run:
        shell: bash
  
    steps:
    - name: ⬇️ Checkout
      uses: actions/checkout@v2
  
    - name: 🔑 'Docker Login'
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - name: 🐳 Docker build
      uses: docker/build-push-action@v4
      with:
        context: ./app
        push: true
        tags:
          ${{ secrets.REGISTRY_LOGIN_SERVER }}/mdc-app:latest
  
  deploy_on_Aks:
    name: '🚀 Deploy App on AKS'
    runs-on: ubuntu-latest
    needs: docker_build_push_acr

    steps:
    - name: '📥 Checkout code'
      uses: actions/checkout@v2

    - name: '🔐 Azure Login'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: '🔑 Get AKS Credentials'
      run: az aks get-credentials --resource-group mdc-rg --name mdc-aks --overwrite-existing

    - name: '🛳 Deploy to AKS'
      run: kubectl apply -f kube-manifest/dev/deployment.yaml

  createPullRequest:
    needs: deploy_on_Aks
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Create Pull Request
      uses: repo-sync/pull-request@v2
      with:
        github_token: ${{ secrets.TOKEN_GB }}
        destination_branch: "homol"  # Merge into this branch
        pr_title: "Automated PR from ${{ github.ref }}"
        pr_body: "## 🚀 Changes Introduced\n\nDescrição das principais mudanças que este PR traz.\n\n## 🖼️ Screenshots\n\n## ⚙️ Testes\n\n## 📦 Dependências\n\n##  Miscelânea \n\n## ☑️ Checklist\n\n- [ ] Código testado\n- [ ] Documentação atualizada\n- [ ] Reviewers alocados\n\n## 🎉 Como testar"
        pr_draft: false
```
12. 02-develop-to-homol.yaml 
```yaml
name: Automate Pull Request - Develop to Homol

on:
  push:
    branches:
      - homol

jobs:

  deploy_on_Aks:
    name: '🚀 Deploy App on AKS'
    runs-on: ubuntu-latest

    steps:
    - name: '📥 Checkout code'
      uses: actions/checkout@v2

    - name: '🔐 Azure Login'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: '🔑 Get AKS Credentials'
      run: az aks get-credentials --resource-group mdc-rg --name mdc-aks --overwrite-existing

    - name: '🛳 Deploy to AKS'
      run: kubectl apply -f kube-manifest/hml/deployment.yaml

  createPullRequest:
    needs: deploy_on_Aks
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Create Pull Request
      uses: repo-sync/pull-request@v2
      with:
        github_token: ${{ secrets.TOKEN_GB }}
        destination_branch: "main"  # Merge into this branch
        pr_title: "Automated PR from ${{ github.ref }}"
        pr_body: "## 🚀 Changes Introduced\n\nDescrição das principais mudanças que este PR traz.\n\n## 🖼️ Screenshots\n\n## ⚙️ Testes\n\n## 📦 Dependências\n\n##  Miscelânea \n\n## ☑️ Checklist\n\n- [ ] Código testado\n- [ ] Documentação atualizada\n- [ ] Reviewers alocados\n\n## 🎉 Como testar"
        pr_draft: false
```
13. **03-homol-to-prod.yaml**
```yaml
name: Automate Pull Request - Homol to main

on:
  push:
    branches:
      - main

jobs:

  deploy_on_Aks:
    name: '🚀 Deploy App on AKS'
    runs-on: ubuntu-latest

    steps:
    - name: '📥 Checkout code'
      uses: actions/checkout@v2

    - name: '🔐 Azure Login'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: '🔑 Get AKS Credentials'
      run: az aks get-credentials --resource-group mdc-rg --name mdc-aks --overwrite-existing

    - name: '🛳 Deploy to AKS'
      run: kubectl apply -f kube-manifest/prd/deployment.yaml
```
14. Create the following directories and files.
```bash
cd kube-manifest
mkdir dev
cd dev
touch deployment.yaml
```
15. Copy the value below into the **dev/deployment.yaml** file.
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: mdc-app-dev
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mdc-app-dev
  namespace: mdc-app-dev
  labels:
    app: mdc-app-dev
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mdc-app-dev
  template:
    metadata:
      labels:
        app: mdc-app-dev
    spec:
      containers:
        - name: mdc-app-dev
          image: mdcregistryid.azurecr.io/mdc-app:latest # CHANGE FOR YOUR REGISTRY
          imagePullPolicy: Always
          ports:
            - containerPort: 5000
          # Requests & Limits   
          resources:
            requests:
              cpu: "100m" 
              memory: "128Mi"
            limits:
              cpu: "200m"
              memory: "256Mi"
          # Readiness Probe
          readinessProbe:
            httpGet:
              path: /success # The endpoint to check for readiness
              port: 5000     # The port to use for the readiness check
            initialDelaySeconds: 5 # The number of seconds to wait before performing the first readiness check
            periodSeconds: 10       # The period (in seconds) between each readiness check

          # Liveness Probe
          livenessProbe:
            httpGet:
              path: /success # The endpoint to check for liveness
              port: 5000     # The port to use for the liveness check
            initialDelaySeconds: 5 # The number of seconds to wait before performing the first liveness check
            periodSeconds: 30       # The period (in seconds) between each liveness check
---
apiVersion: v1
kind: Service
metadata:
  name: mdc-app-dev
  namespace: mdc-app-dev
  labels:
    app: mdc-app-dev
spec:
  type: ClusterIP
  selector:
    app: mdc-app-dev
  ports:
    - port: 80
      targetPort: 5000
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mdc-app-dev
  namespace: mdc-app-dev
  annotations:
    kubernetes.io/ingress.class: nginx
spec:  
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: mdc-app-dev
            port: 
              number: 80


```
16. Create the following directories and files.
```bash
mkdir hml
cd hml
touch deployment.yaml
```
17. Copy the value below into the **hml/deployment.yaml** file.
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: mdc-app-hml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mdc-app-hml
  namespace: mdc-app-hml
  labels:
    app: mdc-app-hml
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mdc-app-hml
  template:
    metadata:
      labels:
        app: mdc-app-hml
    spec:
      containers:
        - name: mdc-app-hml
          image: mdcregistryid.azurecr.io/mdc-app:latest # CHANGE FOR YOUR REGISTRY
          imagePullPolicy: Always
          ports:
            - containerPort: 5000
          # Requests & Limits   
          resources:
            requests:
              cpu: "100m" 
              memory: "128Mi"
            limits:
              cpu: "200m"
              memory: "256Mi"
          # Readiness Probe
          readinessProbe:
            httpGet:
              path: /success # The endpoint to check for readiness
              port: 5000     # The port to use for the readiness check
            initialDelaySeconds: 5 # The number of seconds to wait before performing the first readiness check
            periodSeconds: 10       # The period (in seconds) between each readiness check

          # Liveness Probe
          livenessProbe:
            httpGet:
              path: /success # The endpoint to check for liveness
              port: 5000     # The port to use for the liveness check
            initialDelaySeconds: 5 # The number of seconds to wait before performing the first liveness check
            periodSeconds: 30       # The period (in seconds) between each liveness check
---
apiVersion: v1
kind: Service
metadata:
  name: mdc-app-hml
  namespace: mdc-app-hml
  labels:
    app: mdc-app-hml
spec:
  type: ClusterIP
  selector:
    app: mdc-app-hml
  ports:
    - port: 80
      targetPort: 5000
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mdc-app-hml
  namespace: mdc-app-hml
  annotations:
    kubernetes.io/ingress.class: nginx
spec:  
  rules:
  - http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: mdc-app-hml
            port: 
              number: 80
```
18. Create the following directories and files.
```bash
mkdir prd
cd
touch deployment.yaml
```
19. Copy the value below into the **prd/deployment.yaml** file.
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: mdc-app-prd
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mdc-app-prd
  namespace: mdc-app-prd
  labels:
    app: mdc-app-prd
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mdc-app-prd
  template:
    metadata:
      labels:
        app: mdc-app-prd
    spec:
      containers:
        - name: mdc-app-prd
          image: mdcregistryid.azurecr.io/mdc-app:latest # CHANGE FOR YOUR REGISTRY
          imagePullPolicy: Always
          ports:
            - containerPort: 5000
          # Requests & Limits   
          resources:
            requests:
              cpu: "100m" 
              memory: "128Mi"
            limits:
              cpu: "200m"
              memory: "256Mi"
          # Readiness Probe
          readinessProbe:
            httpGet:
              path: /success # The endpoint to check for readiness
              port: 5000     # The port to use for the readiness check
            initialDelaySeconds: 5 # The number of seconds to wait before performing the first readiness check
            periodSeconds: 10       # The period (in seconds) between each readiness check

          # Liveness Probe
          livenessProbe:
            httpGet:
              path: /success # The endpoint to check for liveness
              port: 5000     # The port to use for the liveness check
            initialDelaySeconds: 5 # The number of seconds to wait before performing the first liveness check
            periodSeconds: 30       # The period (in seconds) between each liveness check
---
apiVersion: v1
kind: Service
metadata:
  name: mdc-app-prd
  namespace: mdc-app-prd
  labels:
    app: mdc-app-prd
spec:
  type: ClusterIP
  selector:
    app: mdc-app-prd
  ports:
    - port: 80
      targetPort: 5000
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mdc-app-prd
  namespace: mdc-app-prd
  annotations:
    kubernetes.io/ingress.class: nginx
spec:  
  rules:
  - http:
      paths:
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: mdc-app-prd
            port: 
              number: 80
```
19. Commit and Push
20. Add, Commit and Push
```bash
git add .
git commit -m "Add automated PR workflows"
git push origin main
```
21. Testing
22. Create a New Feature Branch and Push
```bash
git checkout -b feature/mdc-poc
touch file.txt
git add .
git commit -m "Add file for testing"
git push --set-upstream origin feature/mdc-poc
```
## After the Hands-on lab 

1. Delete all Azure and AWS resources created in support of this Hands-on lab.

1. End of **Workshop Day**

1. Continue in the **Mentoria Arquiteto Cloud & DevOps**.