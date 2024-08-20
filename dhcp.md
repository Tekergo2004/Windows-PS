# DHCP -----> TODO

## DHCP server in a domain

> [!NOTE]
> We will install DHCP service in a domain so, be a domain controller or in a domain!

```powershell
Install-WindowsFeature
    -name 
    -IncludeManagementTools
```

## Standalone server

> [!NOTE]
> Install DFS server in standalone mode.

```powershell
Install-WindowsFeature
    -name 
    -IncludeManagementTools
```
