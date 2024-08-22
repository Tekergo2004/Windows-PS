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
