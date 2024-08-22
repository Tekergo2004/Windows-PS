# FSRM

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

> [!NOTE]
> Create file screening groups and after that create the screenings on the folders (this will screen the extensions in the folder).

```powershell
New-FsrmFileGroup
    -Name "G-Marketing"
    -IncludePattern "*.*"
    -ExcludePattern @("*.doc", "*.docx", "*.xls", "*.xlsx", "*.xml", "*.pdf", "*.txt")
```

```powershell
New-FsrmFileGroup
    -Name "G-HR"
    -IncludePattern "*.*"
    -ExcludePattern @("*.doc", "*.docx", "*.jpg", "*.pdf", "*.txt")
```

```powershell
New-FsrmFileGroup
    -Name "G-IT"
    -IncludePattern "*.exe"
```

> [!NOTE]
> Creation of the file screening.


```powershell
New-FsrmFileScreen
    -Path "C:\Marketing"
    -Description "Marketing"
    -IncludeGroup "G-Marketing"
    -Active
```

```powershell

New-FsrmFileScreen
    -Path "C:\HR"
    -Description "HR"
    -IncludeGroup "G-HR"
    -Active
```

```powershell
New-FsrmFileScreen
    -Path "C:\IT"
    -Description "IT"
    -IncludeGroup "G-IT"
    -Active
```
