# DFS ---> TODO (Standalone)

## ADDFS

> [!NOTE]
> We will install DFS-namespace, and Replication services in a domain so, be a domain controller or be in a domain!

```powershell
Install-WindowsFeature
    -name FS-DFS-Namespace,FS-DFS-Replication
    -IncludeManagementTools
```

> [!NOTE]
> Create the top directory for sharing, and create your other directories for sharing files etc. to IT, HR, Marketing... If you have done it we will share it too.

```powershell
New-Item "C:\Files" -Type Directory

New-SmbShare -Name "Files" -Path "C:\Files" -FullAccess Admins -ReadAccess Employees -ChangeAccess Admins
```

```powershell
New-Item "C:\Marketing",”C:\IT",”C:\HR" -Type Directory

New-SmbShare -Name “Marketing” -Path “C:\Marketing” -FullAccess Admins,Marketing -ChangeAccess Admins
New-SmbShare -Name “HR” -Path “C:\HR” -FullAccess Admins,HR -ChangeAccess Admins
New-SmbShare -Name “IT” -Path “C:\IT” -FullAccess Admins,IT -ChangeAccess Admins
```

> [!NOTE]
> Create DFSN Root if it doesn't exists.

```powershell
New-DfsnRoot -Path "\\TG.NET\Files" -TargetPath "\\WIN-PS.TG.NET\Files" -Type DomainV2
```

> [!NOTE]
> Add directories that have been created earlier.

```powershell
New-DfsnFolder -Path '\\TG.NET\Files\Marketing' -TargetPath '\\WIN-PS.TG.NET\Marketing'
New-DfsnFolder -Path '\\TG.NET\Files\HR' -TargetPath '\\WIN-PS.TG.NET\HR'
New-DfsnFolder -Path '\\TG.NET\Files\IT' -TargetPath '\\WIN-PS.TG.NET\IT'
```

> [!IMPORTANT]
> From there we will configure replication so if you will use just one server for DFS, don't do the next steps.

> [!NOTE]
> You have to repeat the steps, install DFSN and DFS replication, and create and share the directories on your second server, that you will use for replication and after that on the main server you have to create the replication too. Create the replication group and add the your servers into you want to replicate.

```powershell
New-DfsReplicationGroup -GroupName "TG-REP"
```

```powershell
Add-DfsrMember -GroupName "TG-REP" -ComputerName "WIN-PS.TG.HOME", "WIN-REP.TG.HOME"
```

> [!NOTE]
> Add the folders from the DFSN and add a new Path, for them (secondary or third, etc. server)

```powershell
New-DfsReplicatedFolder -GroupName "TG-REP" -FolderName IT,Marketing,HR
```

> [!NOTE]
> You can choose how to create these connections. If you have more servers you can createa it by full mesh or just between some devices, it depends on how many bandwith you have.

```powershell
Add-DfsrConnection
    -GroupName "TG-REP"
    -SourceComputerName "WIN-PS.tg.net"
    -DestinationComputerName "WIN-REP.tg.net"
```

```powershell
Set-DfsrMembership
    -Groupname "TG-REP"
    -FolderName "IT"
    -ComputerName "WIN-PS.tg.net"
    -ContentPath "C:\IT"
    -PrimaryMember $True
    -StagingPathQuotaInMB 16384
    -Force

Set-DfsrMembership
    -Groupname "TG-REP"
    -FolderName "IT"
    -ComputerName "WIN-REP.tg.net"
    -ContentPath "C:\IT"
    -Force
```

```powershell
Set-DfsrMembership
    -Groupname "TG-REP"
    -FolderName "HR"
    -ComputerName "WIN-PS.tg.net"
    -ContentPath "C:\HR"
    -PrimaryMember $True
    -StagingPathQuotaInMB 16384
    -Force

Set-DfsrMembership
    -Groupname "TG-REP"
    -FolderName "HR"
    -ComputerName "WIN-REP.tg.net"
    -ContentPath "C:\HR"
    -Force
```

```powershell
Set-DfsrMembership
    -Groupname "TG-REP"
    -FolderName "Marketing"
    -ComputerName "WIN-PS.tg.net"
    -ContentPath "C:\Marketing"
    -PrimaryMember $True
    -StagingPathQuotaInMB 16384
    -Force

Set-DfsrMembership
    -Groupname "TG-REP"
    -FolderName "Marketing"
    -ComputerName "WIN-REP.tg.net"
    -ContentPath "C:\Marketing"
    -Force
```

> [!NOTE]
> And you're done. This is how you can configure it from powershell.

## Standalone server

> [!NOTE]
> Install DFS server in standalone mode.

```powershell
Install-WindowsFeature
    -name FS-DFS-Namespace,FS-DFS-Replication
    -IncludeManagementTools
```
