[![Open in Dev Containers](https://img.shields.io/static/v1?label=Dev%20Containers&message=Open&color=blue&logo=visualstudiocode)](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/mechdeveloper/demo-hashitalks-india)

If you already have VS Code and Docker installed, you can click the badge above or [here](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/mechdeveloper/demo-hashitalks-india) to get started. Clicking these links will cause VS Code to automatically install the Dev Containers extension if needed, clone the source code into a container volume, and spin up a dev container for use.

# [HashiTalks: India](https://events.hashicorp.com/hashitalksindia)

| | |
|-|-|
| Event Date | January 12, 2023 |
| YouTube | https://youtu.be/NrIfq2FtL4A |

## Deploy Infrastructure as Code on Azure using Github Codespaces and Terraform Cloud

- Azure AD: Setup Azure AD Service Principle for Terraform Cloud 
  - Register Application 
    - Create Client Secret
  - Add role assignment for Azure Subscription
    - Assign Contributor role for Registered Application 

- Terraform: Setup Terraform Cloud 
  - Sign In to terraform cloud 
  - Create New Organization 
    - Create New Workspace 
      - Type: CLI Driven Workflow 
    - Add workspace environment variables 
      ```
      ARM_TENANT_ID
      ARM_CLIENT_ID
      ARM_CLIENT_SECRET
      ARM_SUBSCRIPTION_ID 
      ```

- Codespaces: Deploy linux virtual machines on Azure Infrastructure using Terraform
  - Create codespace on main branch
  - Add DevContainer configuration file
    - Select Ubuntu
    - Select Feature - Terraform, tflint and TFGrunt
  - Create Codespace Secrets for demo repository
    ```
    TF_TOKEN_APP_TERRAFORM_IO
    ```
  - Deploy Infrastructure   
    - `terraform init`
    - `terraform apply`
- Login to your new linux virtual machines from Github codespaces
  - Fetch secret key from terraform output and save it as a file
  - Use secret key to login on newly created Virtual Machine in Azure