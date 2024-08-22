# DHCP ---> TODO

> [!NOTE]
> First there will be some settings for Standalone and ADDS DHCP servers but then it will be the same configuration!

## DHCP server in a domain

> [!NOTE]
> We will install DHCP service in a domain so, be a domain controller or in a domain!

```powershell
Install-WindowsFeature
    -name DHCP
    -IncludeManagementTools
```

> [!NOTE]
> Authorize your DHCP server in a domain

```powershell
Add-DhcpServerInDC -DnsName DHCP1.tg.net -IPAddress 192.168.19.250
```

## Standalone server

> [!NOTE]
> Install DFS server in standalone mode.

```powershell
Install-WindowsFeature
    -name DHCP
    -IncludeManagementTools
```

## Main configuration

> [!NOTE]
> Notify server manager that post-install is done

```powershell
Set-ItemProperty –Path registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ServerManager\Roles\12 –Name ConfigurationState –Value 2
```

> [!NOTE]
> DDNS for clients

```powershell
Set-DhcpServerv4DnsSetting -ComputerName "DHCP1.corp.contoso.com" -DynamicUpdates "Always" -DeleteDnsRRonLeaseExpiry $True\
```

> [!NOTE]
> DHCP server registers or unregisters client records on a DNS server.

```powershell
$Password = ConvertTo-SecureString "Passw0rd" -AsPlainText -Force

rem For stanadlone server
$Credential = New-Object System.Management.Automation.PSCredential("DHCP1\Administrator", $Password)

rem For DHCP server in a domain
$Credential = New-Object System.Management.Automation.PSCredential("TG.NET\Administrator", $Password)

Set-DhcpServerDnsCredential -Credential $Credential -ComputerName "DHCP1.tg.net"
```

> [!NOTE]
> Configure a scope.

```powershell
Add-DhcpServerv4Scope -name "TGNet" -StartRange 192.168.19.10 -EndRange 192.168.19.254 -SubnetMask 255.255.255.0 -State Active
Add-DhcpServerv4ExclusionRange -ScopeID 192.168.19.0 -StartRange 192.168.19.128 -EndRange 192.168.19.254
Set-DhcpServerv4OptionValue -OptionID 3 -Value 192.168.19.1 -ScopeID 192.168.19.0 -ComputerName DHCP1.tg.net
Set-DhcpServerv4OptionValue -DnsDomain tg.net -DnsServer 192.168.19.135
```

> [!NOTE]
> Other methods to confiugre DHCP scope options

```powershell
Set-DhcpServerv4OptionValue
    -ScopeId 192.168.19.0
    -ComputerName "DHCP1.tg.net"
    -DnsServer 192.168.19.135
    -WinsServer 192.168.19.135
    -DnsDomain "tg.net"
    -Router 192.168.19.1
    -OptionId x -Value "xy"
```
