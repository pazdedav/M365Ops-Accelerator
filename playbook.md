# Solution playbook

This is a temporary document for taking notes, jotting ideas and writing key principles.

## Key principles

- Microsoft365Dsc is a POSH DSC module. It can configure and manage M365 in a DevOps way - Configuration as Code
- Changes are done in a Git repository and automatically deployed to a M365 tenant using automated pipeline (GitHub Actions workflow)

## Prerequisites

- **Deployment machine** - self-hosted agent / GitHub runner - will be hosted on Azure. It will do the actual deployment to M365
  - Software: WS 2016+, .NET Framework 4.7+, PowerShell 5.1, Up-to-date PowerShellGet: `Install-PackageProvider Nuget -Force; Install-Module -Name PowerShellGet -Force`
  - local account with admin privileges
- **GitHub repository** - e.g., `M365Config` - a private repository that will be used for the automated deployment pipeline and storing the configuration files. It will be created from the "Accelerator" template!
- **Microsoft 365 tenant** - will be managed using Microsoft365Dsc
  - a Global Administrator privileges (to access the Admin Portal)
  - a service account - `DscConfigAdmin` - with Global Administrator privileges (to deploy settings using DSC) - cannot be configured for MFA; no license assignment needed; _Note: could have more limited permissions depending on the resources in your configuration._

## Bootstrapping workflow

[ ] - Q: Assume that GitHub repository is created already or create it with e.g., gh CLI or otherwise (?)
[ ] - Q: Explore the possibility to use OICD integration with AAD or if we need to have SPN and Secrets in the GitHub repository

1. Create an account in Microsoft 365 Admin Portal with Global Administrator privileges (DSCConfigAdmin). _Note: is this step (3.1) redundant with 3.6.1?_
1. Create a new self-hosted runner "pool" (single or vmss) config in the repo (agent pools in ADO, example name: Microsoft365Dsc).
1. Create a PAT token (Name: DevOpsAgent, expiration: 1 year, Scope: Agent pools: Read & manage), copy the secret (token).
1. Provision a new VM in Azure - add 'CustomScript' extension to:
    1. Ensure all prerequisites (software) are installed
    1. Download and uzip the "agent" package to c:\agent directory
    1. Create a local service account for the agent (account needs local Admin permissions for LCM)
    1. Run the `config.cmd` with an elevated cmd prompt - the script with require parameters to register the runner to the pool (repo/project name, PAT token, agent pool name, agent server name, run as service: Yes; Enter created service account credentials)
1. Create SPN: `az ad sp create-for-rbac -n Microsoft365Dsc` - save the output.
1. Create a new Key Vault (Name: M365DscAzureKeyVault or similar); standard tier; soft-delete: yes; purge-protection: enabled
1. Grant Secrets: Get+List permissions to the SPN (Microsoft355Dsc)
1. Add secret: Name=DscConfigAdmin; Value=password of the DSC account in M365 tenant.
1. Add Service connection (name: KeyVaultConnection): Azure Resource Manager - Service Principal (manual); use info from the AZ CLI output for Microsoft365Dsc SPN; subscriptionId: write a specific one; grant permission to all pipelines: yes

//Stopped on page 27 (section 3.7)