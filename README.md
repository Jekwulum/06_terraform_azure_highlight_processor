# Bonus Terraform Game Highlight Processor
## 06_terraform_azure_highlight_processor
**Week 2 - Day 4:** Provisioning Azure resources to fetch and store NCAA game highlights

![Project Structure](05_azure_highlight_processor.drawio.png)

## Project Overview
Just like the previous project: [05_azure_highlight_processor](https://github.com/Jekwulum/05_azure_highlight_processor), this project also uses RapidAPI to obtain NCAA game highlights, stores the json file in an Azure Blob and then parses the json file for a video url and downloads the video to the same Azure Storage Blob. The difference is that the resources on Azure are provisioned using the IaC (Infrastructure-as-Code) tool **terraform**

## Features
- Azure Container Apps (for running the containerized application)
- Programming Language: Python 3.x
- IaC: Terraform

## Prerequisites
- An Azure account with sufficient permissions to create resources
- Azure Service Principal for authentication
- Azure CLI installed and configured
- Rapid API key
- Terraform installed

## Dependencies
- requests==2.28.1
- azure-storage-blob==12.14.1
- azure-identity==1.13.0
- azure-mgmt-resource==21.1.0
- azure-mgmt-storage==19.0.0
- python-dotenv

## Project Structure
```shell
05_azure_highlight_processor/
├── terraform/
│   ├── main.tf
│   ├── outputs.tf
│   ├── storage.tf
│   ├── terraform.tfvars
│   └── variables.tf
├── .gitignore
├── .env
├── config.py
├── fetch.py
├── process_one_video.py
├── run_all.py
├── update_env.py
├── requirements.txt
└── README.md
```

## File Overview
`terraform files` <br><br>
`main.tf`: Defines the Azure provider, configures authentication, and creates a resource group.

`outputs.tf`: Outputs key details like the resource group name, storage account name, and primary access key (marked as sensitive).

`storage.tf`: Creates an Azure storage account and a private blob container for storing data.

`variables.tf`: Declares input variables for configuration, such as service principal details, resource group name, and storage account settings. <br><br>

`python files` <br><br>
`config.py`: Centralizes configuration settings, loading environment variables for API, Azure Storage, and retry logic.

`update_env.py`: Updates the .env file with Azure Storage details (account name, key, container) from Terraform outputs.

`fetch.py`: Fetches sports highlights from a RapidAPI endpoint and saves the JSON data to Azure Blob Storage.

`run_all.py`: Orchestrates the execution of scripts (fetch.py, process_one_video.py) with retry logic and delays between steps.

# steps
1. Clone the repository
  ```shell
    git clone git@github.com:Jekwulum/06_terraform_azure_highlight_processor.git
    cd 06_terraform_azure_highlight_processor
  ```
2. Set the environment variables in the `.env` file as seen in the `.env.example` file
3. Setup the Python environment
  ```shell
    # windows
    python -m venv venv
    venv\Scripts\activate
  ```
  ```shell
    # macos/linux
    python3 -m venv venv
    source venv/bin/activate
  ```
4. Install Project Dependencies
  ```shell
    pip install --upgrade pip
    pip install -r requirements.txt
  ```
5. Login to Azure CLI
  ```shell
    az login
  ```
6. create a service principal for the automation of the terraform
  ```shell
    az ad sp create-for-rbac --name <your-service-principal-name>
  ```
  This generates the following sample credentials which should be set in the `terraform.tfvars` file
  ```shell
    {
      "appId": "myAppId", # appId becomes client_id
      "displayName": "myServicePrincipalName",
      "password": "myServicePrincipalPassword", # password becomes client_secret
      "tenant": "myTentantId" # tenant becomes tenant_id
    }
  ```
7. Provision Azure Infrastructure with Terraform
  ```shell
    terraform init # Download providers and set up Terraform.

    terraform fmt # Format config files for readability.

    terraform validate # Check config for errors.

    terraform plan # Preview changes Terraform will make.

    terraform apply # Apply changes (requires confirmation).

    terraform apply -auto-approve # Apply changes without confirmation.
  ```
8. Update .env file automatically
  ```shell 
    python update_env.py
  ```
  This script will update the following variables in your .env:
  -  AZURE_STORAGE_ACCOUNT_NAME
  -  AZURE_STORAGE_ACCOUNT_KEY
  -  AZURE_BLOB_CONTAINER_NAME

9. Execute The Data Processing Pipeline
  ```shell
    python run_all.py
  ```