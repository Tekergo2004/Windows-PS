# Group Policy ---> TODO

> [!NOTE]
> I will configure Group Policy Objects in a domain, but you can find these in a local envriotment without domain services too. <br>If you are using computers in your domain, use this command, to your rules be available on your computers.

```powershell
gpupdate /force

# or use this

Invoke-GPUpdate -Computer "*"
```

## Default settings for a server envritoment

> [!NOTE]
> There area some settings that I use until configuring a windows server. Ensure that it is available.

```powershell
Import-Module GroupPolicy
```

> [!NOTE]
> Create a new GPO

```powershell
# Get the GPO by name, or create it if it doesn't exist
New-GPO -Name "TG Domain Policies" -Comment "GPO settings for my organization"
New-GPLink -Name "TG Domain Policies" -Target "DC=tg,DC=net"
```

> [!NOTE]
> Disable CTRL+ALT+DEL login (`Computer Configuration\Windows Settings\Security Settings\Local Policies\Security Options`)

```powershell
Set-GPRegistryValue -Name "TG Domain Policies" -Key "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" -ValueName "DisableCAD" -Type DWord -Value 0
```

> [!NOTE]
> Disable first logon animation (`Computer Configuration\Administrative Templates\System\Logon`)

```powershell
Set-GPRegistryValue -Name "TG Domain Policies" -Key "HKLM\Software\Policies\Microsoft\Windows\System" -ValueName "EnableFirstLogonAnimation" -Type DWord -Value 0
```

> [!NOTE]
> Enable ICMP all over the computers (`Computer Configuration\Policies\Windows Settings\Security Settings\Windows Defender Firewall with Advanced Security\Windows Defender Firewall with Advanced Security - LDAP`)

```powershell
Set-GPRegistryValue -Name "TG Domain Policies" -Key HKLM\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile\GloballyOpenPorts\List -ValueName "7:IPv4" -Type String -Value "7:TCP:7:*:Enabled:@FirewallAPI.dll,-28502"

Set-GPRegistryValue -Name "TG Domain Policies" -Key HKLM\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile\GloballyOpenPorts\List -ValueName "7:IPv6" -Type String -Value "7:TCP:7:*:Enabled:@FirewallAPI.dll,-28502"
```


# FROM THIS NOT WORKING


> [!NOTE]
> First create the directory and copy the `.msi` packages into that folder, what you have shared. After that deploy this package(s). Because there is no powershell script for this we will deploy a powershell script that runs the 'deployment'.(`Computer Configuration\Policies\Software Settings\Software installation`)

```powershell
New-Item "C:\Software" -ItemType Directory
New-SmbShare -Name "Software" -Path "C:\Software" -FullAccess Domain Admins -ReadAccess Everyone
```

> [!NOTE]
> If you want to have more script deployment, and you will want... than you have to create a folder for that you will share.

```powershell
New-Item "C:\StScripts" -ItemType Directory
New-SmbShare -Name "StScripts" -Path "C:\StScripts" -FullAccess Domain Admins -ReadAccess Everyone
```

`C:\Scripts\deploy.ps1`

```powershell
msiexec.exe /i /passive /quiet \\WIN-PS\Software\firefox.msi
```

> [!NOTE]
> You can add startup scripts here: `Computer Configuration\Policies\Windows Settings\Scripts (Startup/Shutdown)`

```powershell
Set-GPShutdownScript -ScriptName "deploy.ps1" -ScriptPath "\\WIN-PS\StScripts\deploy.ps1" -GPOName "TG"
```

> [!NOTE]
> Add a shared directory as network shared directory to every computer in your domain. (If you want to give it to just someone create a GPO linking to an ou and set it.) Use DFSN or create a new share. (`User Configuration\Preferences\Windows Settings\Drive Maps`)

```powershell
New-GPRegistryValue -Name "TG Domain Policies" -Key "HKCU\Software\Policies\Microsoft\Windows\Network Connections" -ValueName "NC_AllowNetSetup" -Type DWord -Value 1; New-GPDriveMapping -DriveLetter "H:" -Location "\\TG.NET\Files" -Reconnect $true
```
