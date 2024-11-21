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
  
## Infras As Code (Bicep)
### DevBox Templates Bicep creation  [DevBox-Demo](https://github.com/gbordier/devbox-demo)

