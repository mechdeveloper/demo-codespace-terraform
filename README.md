[![Open in Dev Containers](https://img.shields.io/static/v1?label=Dev%20Containers&message=Open&color=blue&logo=visualstudiocode)](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/mechdeveloper/demo-hashitalks-india)

If you already have VS Code and Docker installed, you can click the badge above or [here](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/mechdeveloper/demo-hashitalks-india) to get started. Clicking these links will cause VS Code to automatically install the Dev Containers extension if needed, clone the source code into a container volume, and spin up a dev container for use.

# [HashiTalks: India](https://events.hashicorp.com/hashitalksindia)

| Topic | Description |
|-|-|
| Event Date | January 12, 2023 |
| YouTube - HashiCorp | <https://youtu.be/ByCeAQlJQvM> |
| YouTube - Ashish Singh Baghel | <https://youtu.be/0pNZ2UWkPfQ> |

## Deploy Infrastructure as Code on Azure using Github Codespaces and Terraform Cloud

### Concepts 

__Azure service principal__

- [Azure Built-in roles](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles)
- [Create an Azure service principal with the Azure CLI](https://learn.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli)
- [Apps and service principal objects in Azure Active Directory](https://learn.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)
- [Securing cloud-based service accounts](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/service-accounts-introduction-azure#service-principals)

__Terraform Cloud__

- [What is Terraform Cloud?](https://developer.hashicorp.com/terraform/cloud-docs)
- [Get Started Azure](https://developer.hashicorp.com/terraform/tutorials/azure-get-started)
- [Store Remote State](https://developer.hashicorp.com/terraform/tutorials/azure-get-started/azure-remote)

__Github CodeSpaces__

- [Developing in a codespace](https://docs.github.com/en/codespaces/developing-in-codespaces/developing-in-a-codespace)
- [Introduction to dev containers](https://docs.github.com/en/codespaces/setting-up-your-project-for-codespaces/introduction-to-dev-containers)

### Hands-On 

- Azure AD: Create a Service Principal with a Client Secret
  
  Automated tools that use Azure services should always have restricted permissions. Instead of having applications sign in as a fully privileged user, Azure offers service principals.

  An Azure service principal is an identity created for use with applications, hosted services, and automated tools to access Azure resources. This access is restricted by the roles assigned to the service principal, giving you control over which resources can be accessed and at which level. For security reasons, it's always recommended to use service principals with automated tools rather than allowing them to log in with a user identity.

  - Ensure you are using correct subscription
    
    List subscriptions
    ```
    az account list
    ```
    
    Set a subscription to be the current active subscription
    ```
    az account set --subscription=SUBSCRIPTION_ID​
    ```
    
    Check current subscription
    ```
    az account show
    ```
    
  - Create a service principal and configure its [Contributor](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#contributor) access to azure subscription
    
    > ⚠ Warning: When you create an Azure service principal using the `az ad sp create-for-rbac` command, the __output includes credentials that you must protect__. Be sure that you do not include these credentials in your code or check the credentials into your source control.

    ```
    # fetch subscription id
    SUBSCRIPTION_ID=$(az account show --query id -o tsv)
    
    # Create service principal
    az ad sp create-for-rbac --name TerraformCloud --role Contributor --scopes /subscriptions/$SUBSCRIPTION_ID
    ```

    The output for a service principal with password authentication includes the `password` key. __Make sure you copy this value__ - it can't be retrieved. If you lose the password, [reset the service principal credentials](https://learn.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli#6-reset-credentials).

    Output: 
    ```
    Creating 'Contributor' role assignment under scope '/subscriptions/<yourSubscriptionId>'
    The output includes credentials that you must protect. Be sure that you do not include these credentials in your code or check the credentials into your source control. For more information, see https://aka.ms/azadsp-cli
    {​
      "appId": "00111232-4466-6546-7897-7412321598756547",
      ​"displayName": "TerraformCloud",​
      "password": ".XX1X~Xxxx1XXxXXxXxxxxXX1xxxXXXxXXXXxxxX",​
      "tenant": "11111111-1111-1111-1111-111111111111"​
    }
    ```

    This creates an application object and a service principal object in your home tenant. Any changes that you make to your application object are also reflected in its service principal object in the application's home tenant only (the tenant where it was registered). This means that deleting an application object will also delete its home tenant service principal object.
    
    List app 
    ```
    az ad app list --display-name TerraformCloud -o table
    ```

    Check your service principal
    ```
    az ad sp list --display-name TerraformCloud -o table
    ```

    Check role assignment
    ```
    SP_TERRAFORM_APP_ID=$(az ad sp list --display-name TerraformCloud --query "[].appId" -o tsv)
    az role assignment list --assignee $SP_TERRAFORM_APP_ID
    ```

- Terraform: Setup Terraform Cloud 
  - Sign In to Terraform Cloud - <https://app.terraform.io/>
  - Create New Organization 
  - Create variable set __Azure Credentials__ - Apply to all workspaces
    | Key | Value | Category | 
    |-|-|-|
    | `ARM_TENANT_ID` | `tennant` | Environment Variable |
    | `ARM_SUBSCRIPTION_ID` | `SUBSCRIPTION_ID` | Environment Variable |
    | `ARM_CLIENT_ID` | `appId` | Environment Variable |
    | `ARM_CLIENT_SECRET` | `password` | Environment Variable |

    > Note: We get these values as output of Azure CLI create service principal command. If you lose If you lose the credentials for a service principal, use `az ad sp credential reset`. The reset command takes the same parameters as `az ad sp create-for-rbac`.
    ```
    az ad sp credential reset --name TerraformCloud --role Contributor --scopes /subscriptions/$SUBSCRIPTION_ID
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
    
    __Optional: Codespace Secrets__

    Create [Codespace Secrets](https://docs.github.com/en/codespaces/managing-your-codespaces/managing-encrypted-secrets-for-your-codespaces). Secrets are environment variables that are encrypted and only exposed to Codespaces you create. You can store sensitive information, like tokens, that you want to access in your codespaces via environment variables. 
    Once you have created a secret, it will be available when you create a new codespace or restart the codespace.

    [Terraform CLI Environment Variable Credentials](https://developer.hashicorp.com/terraform/cli/config/config-file#environment-variable-credentials)

    The value of a variable named `TF_TOKEN_app_terraform_io` will be used as a bearer authorization token when the CLI makes service requests to the hostname `app.terraform.io`

      ```
      TF_TOKEN_app_terraform_io
      ```

  - Define `cloud` block for Terraform Cloud Integration
    ```tf
    terraform {​
      required_version = ">=1.3.0"​
      cloud {​
        organization = "<Your-Terraform-Cloud-Organization-Name-Here>"
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
  
  - Destroy Infrastructure 
    Important Note: Do not forget to destroy your unused infrastructure to save cost
    ```
    terraform destroy
    ```

## Clean Up

- Azure: Clean Up service principal and role assignment

  Delete Role Assignment 
  ```
  # fetch `appId` of service principal
  SP_TERRAFORM_APP_ID=$(az ad sp list --display-name TerraformCloud --query "[].appId" -o tsv)
  
  # List Role Assignment
  az role assignment list --assignee $SP_TERRAFORM_APP_ID 
  
  # Delete Role Assignment
  az role assignment delete --assignee $SP_TERRAFORM_APP_ID 
  ```

  Delete Application object (This will also remove Service Principal)
  ```
  # List Regsitered App
  az ad app list --display-name TerraformCloud -o table

  # List Service Principal
  az ad sp list --display-name TerraformCloud -o table

  # fetch Object ID of Application
  TERRAFORM_APP_OBJECT_ID=$(az ad app list --display-name TerraformCloud --query "[].id" -o tsv)

  # Show Application
  az ad app show --id $TERRAFORM_APP_OBJECT_ID

  # Delete Application
  az ad app delete --id $TERRAFORM_APP_OBJECT_ID
  ```
