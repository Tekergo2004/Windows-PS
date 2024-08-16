# ADDS

## 1. step: Installation

> [!NOTE]
> Install ADDS and DNS services.

```ps
Install-WindowsFeature -name AD-Domain-Services,DNS -IncludeManagementTools
```

## 2. step: Deployment

> [!NOTE]
> Create a new forest.

```ps
Install-ADDSForest -DomainName "tg.net" -SafeModeAdministratorPassword (ConvertTo-SecureString "Passw0rd" -AsPlainText -Force) -Force -InstallDNS -DatabasePath "C:\Windows\NTDS" -LogPath "C:\Windows\NTDS" -SysvolPath "C:\Windows\SYSVOL" 
```

> [!NOTE]
> Create a RODC.

```ps
Install-ADDSDomainController -DomainName "tg.net" -InstallDNS -Credential (Get-Credential) -ReadOnlyReplica -NoGlobalCatalog:$false -SafeModeAdministratorPassword (ConvertTo-SecureString "Passw0rd" -AsPlainText -Force) -Force -InstallDNS -DatabasePath "C:\Windows\NTDS" -LogPath "C:\Windows\NTDS" -SysvolPath "C:\Windows\SYSVOL"
```

> [!NOTE]
> Create a sub domain.

```ps
Install-ADDSDomain -NewDomainName "jedlik.tg.net" -ParentDomainName "tg.net" -InstallDNS -SafeModeAdministratorPassword (ConvertTo-SecureString "Passw0rd" -AsPlainText -Force) -Force -InstallDNS -DatabasePath "C:\Windows\NTDS" -LogPath "C:\Windows\NTDS" -SysvolPath "C:\Windows\SYSVOL"
```
