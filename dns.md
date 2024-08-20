# DNS

## Install services

> [!NOTE]
> Install DNS service if you haven't already done it. You can use it in standalone mode and with adds. Adds uses DNS, so if you install it later, you will not have any conflicts with it.

```powershell
Install-WindowsFeature `
    -name DNS
    -IncludeManagementTools
```

## Default settings

> [!NOTE]
> By deafult it listens on all existing ip addresses, but you can set on which interface to listen. You can check it by writing `Get-DNSServerSetting -ALL` on administrator powershell. You can set by these commands: (With this you will just set IPv4 address, so add your IPv6 address if needed.)

```powershell
$DnsServerSettings = Get-DnsServerSetting -ALL
$DnsServerSettings.ListeningIpAddress = @("192.168.1.8")
Set-DNSServerSetting $DnsServerSettings
 ```

## Primary zone

> [!NOTE]
> Create primary zone

```powershell
netsh 
```

## Reverse zone

> [!NOTE]
> Something to describe what I'am doing.

```powershell
netsh 
```

## Stub zone

> [!NOTE]
> Something to describe what I'am doing.

```powershell
netsh 
```

## Replication

> [!NOTE]
> Something to describe what I'am doing.

```powershell
netsh 
```
