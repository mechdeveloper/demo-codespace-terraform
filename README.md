[![Open in Dev Containers](https://img.shields.io/static/v1?label=Dev%20Containers&message=Open&color=blue&logo=visualstudiocode)](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/mechdeveloper/demo-hashitalks-india)

If you already have VS Code and Docker installed, you can click the badge above or [here](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/mechdeveloper/demo-hashitalks-india) to get started. Clicking these links will cause VS Code to automatically install the Dev Containers extension if needed, clone the source code into a container volume, and spin up a dev container for use.

# [HashiTalks: India](https://events.hashicorp.com/hashitalksindia)

| Topic | Description |
|-|-|
| Event Date | January 12, 2023 |
| YouTube | https://youtu.be/NrIfq2FtL4A |

## Deploy Infrastructure as Code on Azure using Github Codespaces and Terraform Cloud

- Terraform: Setup Terraform Cloud 
  - Sign In to terraform cloud 
  - Create New Organization 
  - Create variable set __Azure Credentials__ - Apply to all workspaces
    | Key | Value | Category | 
    |-|-|-|
    | `ARM_TENANT_ID` | `tennant` | Environment Variable |
    | `ARM_SUBSCRIPTION_ID` | `SUBSCRIPTION_ID` | Environment Variable |
    | `ARM_CLIENT_ID` | `appId` | Environment Variable |
    | `ARM_CLIENT_SECRET` | `password` | Environment Variable |

- Azure AD: Create a Service Principal with a Client Secret
  - list subscriptions
    ```
    az account list
    ```
  - set a subscription to be the current active subscription
    ```
    az account set --subscription=SUBSCRIPTION_ID​
    ```
  - create a service principal and configure its access to azure resources
    ```
    az ad sp create-for-rbac \​
        --display-name="Terraform" \​
        --role="Contributor" \​
        --scopes="/subscriptions/SUBSCRIPTION_ID"​
    
    {​
      "appId": "00111232-4466-6546-7897-7412321598756547",
      ​"displayName": "Terraform",​
      "password": "7521-2314-7169-9658-456574136987",​
      "tenant": "11111111-1111-1111-1111-111111111111"​
    }
    ```
    
    Check your service principal
    ```
    az ad sp list --display-name Terraform
    ```
    Output appId of service principal 
    ```
    az ad sp list --display-name Terraform --query "[].appId" -o tsv
    ```

    Check role assignment
    ```
    az role assignment list --asignee <appId>
    ```
      
- GitHub Codespaces: Create codespaces for your GitHub repository
  - Add DevContainer configuration file
    - Select Ubuntu base image
    - Select Feature - Terraform, tflint and TFGrunt
    - Rebuild container
  
  - Login to Terraform Cloud
    ```
    terraform login
    ```
    Create Terraform User API Token (User settings > Tokens)

  - Optional: Create Codespace Secrets for your repository
    ```
    TF_TOKEN_APP_TERRAFORM_IO
    ```
  - Define `cloud` block for Terraform Cloud Integration
    ```tf
    terraform {​
      required_version = ">=1.3.0"​
      cloud {​
        organization = "organization-name"​
        workspaces {​
          name = "workspace-name"​
        }​
      }​
      ...​
      ...​
      ...​
    }​
    ```
    This will create CLI-driven Terraform Cloud workflow, this allows developers to quickly iterate on configuration and work locally.

  - Initialize the configuration and create your new Terraform Cloud workspace
    ```
    terraform init
    ```
    
  - Create Infrastructure
    ```
    terraform apply
    ```
