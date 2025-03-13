<html>
<meta name="google-site-verification" content="O6a3XUVrky2UH1ImDuQMycZhvLMpAyacOcNat0iIdhk" />
</html>

Those pages by Guillaume Bordier try to collect different assets and explanation, usually supported by scripts or home-made tools.

# Command Line  [Tools](https://github.com/gbordier/tools)
Old command line tools pre-dating powershell and originally built for very old version of Windows, most of those still work and several have been updated along the years such as [fileacl](https://github.com/gbordier/tools/blob/main/fileacl/FILEACL.md)


# Azure related assets
## Powershell 
### [CopyVMCrossTenant](https://github.com/gbordier/CopyVMCrossTenant) is a tool to copy Azure Virtual Machines from One Tenant to the Other while doing some modifications 
- transform blob storage disks to Managed disks
- update dns info
- create underlying vnets and subnets
### Running Powershell on Linux
These days, running pwsh on Linux has never been so easy, there are still several ways of doing this.
Microsoft documents this [here](https://learn.microsoft.com/en-us/dotnet/core/install/linux) 

On ARM machine such as Rasberry PI it can be a bit more tricky and you may need to rebuild it from scratch, but it works well after that.

the easiest way I found is to actually run it inside docker, the microsoft [repository]([https://mcr.microsoft.com/powershell](https://mcr.microsoft.com/en-us/artifact/mar/powershell) However for distributions such as debian on ARM (raspbian) those distribution do not work, but we can use the SDKs images instead such as.
```
docker run -it mcr.microsoft.com/dotnet/sdk:9.0 pwsh
```
which will run pwsh nicely.

## Entra ID Authentication 
Entra ID (formerly known as Azure Active Directory) is the Identity provider for all Microsoft Cloud workloads.

### [Machine 2 Machine Authentication](https://github.com/gbordier/AADM2MAuth/blob/main/M2MAUTH.md) is a documentation that explain the details of intra or cross-tenant Machine 2 Machine Authentication with Entra ID.

### [AADM2MAuth Demo](https://github.com/gbordier/AADM2MAuth/blob/main/README.md) has the code to repro a demo of cross tenant M2M authentication

## Infra As Code (Terraform) 
### Azure RM and Azure API providers updates
Background : the azurerm provider handles azure resource manager API interaction for terraform.
authentication always has been a weird topic with the mix of interactive, service principal, managed service identity and the backend (handling terraform state) somewhat using different mechanism.
now with the advent of terraform v1.11 and [azurerm](https://github.com/hashicorp/terraform-provider-azurerm) provider starting at around version 4.0.

Now Azure CLI (az login) can be used to authenticate both to azure backend and azurerm provider and azapi provider, including service principal authentication with federated credentials.
simply put, terraform developper can now test their code from the command line using az login and use the federated authentication with  github action using the AzLoginv2 task without changing a single line of tf code.
a demo for this is in my [azurerm test repo](http://github.com/gbordier/azurerm-test)
a short summary here
``` yaml
    - name: Azure login
      uses: azure/login@v2
      with:
         client-id: ${{ needs.get_config.outputs.client_id }}
         tenant-id: ${{ needs.get_config.outputs.tenant_id }}
         subscription-id: ${{ needs.get_config.outputs.subscription_id }}
         enable-AzPSSession: false
    - name: 'init terraform'
      working-directory: "tf"
      env:
        ARM_SUBSCRIPTION_ID: ${{ needs.get_config.outputs.subscription_id }}
        ARM_USE_AZUREAD: true ## to acces TF backend storage account with Entra ID 
      run: |
         terraform init -backend-config="use_azuread_auth=true" \
          -backend-config="storage_account_name=${{ needs.get_config.outputs.tf_storage_account_name }}" \
          -backend-config="resource_group_name=${{ needs.get_config.outputs.tf_resource_group_name }}" \
          -backend-config="container_name=${{ needs.get_config.outputs.tf_container_name }}" \
          -backend-config="key=tfstate.${currentmodule}"

    - name: Terraform Plan
      working-directory: ${{ env.TF_DIRECTORY }}
      env:
        ARM_SUBSCRIPTION_ID: ${{ needs.get_config.outputs.subscription_id }}
        ARM_USE_AZUREAD: true ## to acces TF storage account
      run: |
        echo "using configfile ${{ needs.get_config.outputs.variables_file }}"
        terraform plan -var-file=../${{ needs.get_config.outputs.variables_file }} \
          -out="out.plan"

    - name: Terraform Apply
      working-directory: ${{ env.TF_DIRECTORY }}
      env:
        ## this replaces -backend-config setup for OIDC 
        ## no need to give the CLIENT ID, USE_OIDC everything is inherited from the az login
        ## terraform retrieves the access token from "az account get-access-token"
        ARM_SUBSCRIPTION_ID: ${{ needs.get_config.outputs.subscription_id }}
        ARM_TENANT_ID: ${{ needs.get_config.outputs.tenant_id }}
        ARM_USE_AZUREAD: true ## to acces TF storage account
      run: |
        terraform apply ./out.plan
  
```

## Infras As Code (Bicep)

### DevBox Templates Bicep creation  [DevBox-Demo](https://github.com/gbordier/devbox-demo)

