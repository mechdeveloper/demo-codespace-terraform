# Deploy Infrastructure as Code on Azure using Github Codespaces and Terraform Cloud

- Setup Github Codespaces, Install Developer tools
  - Git
  - Terraform CLI
- Setup Terraform Cloud 
  - Sign In to terraform cloud
  - Create Workspace
- Setup Azure AD Service Principle for Terraform Cloud Authentication
  - Register Application
- Deploy linux virtual machines on Azure Infrastructure using Terraform
  - `terraform init`
  - `terraform plan`
  - `terraform apply`
- Login to your new linux virtual machines from Github codespaces
  - Fetch secret key from terraform output and save it as a file
  - Use secret key to login on newly created Virtual Machine in Azure