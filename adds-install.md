# ADDS

## 1. step: Installation

> [!NOTE]
> Install ADDS and DNS services.

```powershell
Install-WindowsFeature
    -name AD-Domain-Services,DNS
    -IncludeManagementTools
```

> [!NOTE]
> Install schmmgmt, so you can use later ADDS snap-ins in mmc if you want to configure over GUI.

```powershell
regsvr32.exe schmmgmt.dll
```

## 2. step: Deployment

> [!NOTE]
> Create a new forest.

```powershell
Install-ADDSForest
    -DomainName "tg.net"
    -SafeModeAdministratorPassword (ConvertTo-SecureString "Passw0rd" -AsPlainText -Force)
    -Force
    -InstallDNS
    -DatabasePath "C:\Windows\NTDS"
    -SysvolPath "C:\Windows\SYSVOL"
    -LogPath "C:\Windows\NTDS" 
```

> [!NOTE]
> Create a RODC.

```powershell
Install-ADDSDomainController
    -DomainName "tg.net"
    -InstallDNS
    -Credential (Get-Credential)
    -ReadOnlyReplica
    -NoGlobalCatalog:$false
    -SafeModeAdministratorPassword (ConvertTo-SecureString "Passw0rd" -AsPlainText -Force)
    -Force
    -DatabasePath "C:\Windows\NTDS"
    -SysvolPath "C:\Windows\SYSVOL"
    -LogPath "C:\Windows\NTDS" 
```

> [!NOTE]
> Create a sub domain.

```powershell
Install-ADDSDomain
    -NewDomainName "jedlik.tg.net"
    -ParentDomainName "tg.net"
    -InstallDNS
    -SafeModeAdministratorPassword (ConvertTo-SecureString "Passw0rd" -AsPlainText -Force)
    -Force
    -DatabasePath "C:\Windows\NTDS"
    -SysvolPath "C:\Windows\SYSVOL"
    -LogPath "C:\Windows\NTDS" 
```
