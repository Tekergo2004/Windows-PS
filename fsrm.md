# FSRM -----> TODO

## Install services

> [!NOTE]
> FSRM is a tool for make restrictions and rules for storage space, and file extensions.

```powershell
Install-WindowsFeature
    -name FS-Resource-Manager
    -IncludeManagementTools
```

> [!NOTE]
> Create quotas, that creates a hard or soft link to a folder in storage space. I use my folders from DFSN configuration so if you don't have folders like me just create them (and share if you want to use it as a shared directory over your domain)!

```powershell
New-FsrmQuota
    -Path "C:\Marketing"
    -Description "limit usage to 20 GB."
    -Size 20GB
```

```powershell
New-FsrmQuota
    -Path "C:\HR"
    -Description "limit usage to 20 GB."
    -Size 20GB
```

```powershell
New-FsrmQuota
    -Path "C:\IT"
    -Description "limit usage to 100 GB."
    -Size 100GB
```
