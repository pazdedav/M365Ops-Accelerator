# Bootstrap flow

## Prepare GitHub repo
- create a new repo from the "Accelerator" template (in an organization account)
  - template must contaion: Download the DSC scripts from https://aka.ms/M365DSCWhitepaper/Scripts
  - template contains: GitHub Actions workflows
    - scheduled Compliancy Test Release workflow
    - Build workflow
    - Release workflow

- Create a new self-hosted runner "pool" (single or vmss) config in the repo (Microsoft365DSC)


## Prepare M365 tenant
- GUI-based ?
- 4.2.1 Create an account for DSC
- 4.2.2 Create an app registration in AAD (+ Add Exchange Admin role + add reader permission to the Azure sub.)


## Deploy self-hosted runner (Azure VM with subresources)
- bicep template for VM, VNet, NIC, disk, Key Vault
- custom script extension with prereqs:
  - 4.1.1 PowerShell requirements
  - 4.1.2 GH Agent service account
  - 4.1.3 M365 authN certificate
  - 4.1.4 LCM certificate
  - 4.1.5 GH Agent installation and registration (with PAT token??)

## Finalize GH configuration
- New GH secret (in ADO service connection) or OIDC integration with AAD


