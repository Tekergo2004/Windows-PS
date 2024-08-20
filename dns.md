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

## Forward zone

> [!NOTE]
> Create primary zone, if you don't have already one on another server. If you do, then go to Stub zone and replication. (Hostname -> IP)

```powershell
Add-DnsServerPrimaryZone `
    -Name "tg.home" `
    -ZoneFile "tg.home.dns" `
    -DynamicUpdate None
```

> [!NOTE]
> You can add records into your zone like this.

```powershell
# Add an A record
Add-DnsServerResourceRecordA `
    -ZoneName "tg.home" `
    -Name "www" `
    -IPv4Address "192.168.1.200"

    # Add a CNAME record
Add-DnsServerResourceRecordCName `
    -ZoneName "tg.home" `
    -Name "mail" `
    -HostNameAlias "www.tg.home"

# Add an MX record
Add-DnsServerResourceRecordMX `
    -ZoneName "tg.home" `
    -Name "@" `
    -MailExchange "mail.example.com" `
    -Preference 10
```

## Reverse zone

> [!NOTE]
> Create primary zone, if you don't have already one on another server. If you do, then go to Stub zone and replication. (IP -> Hostname)

```powershell
netsh 
```

## Stub zone

> [!NOTE]
> Something to describe what I'am doing.

```powershell
netsh 
```

## Forwarder

> [!NOTE]
> Set up another ip forwarder DNS server. (A server that will provide DNS lookup if yours don't know what the client looking for.)

```powershell
Add-DnsServerForwarder `
    -IPAddress "8.8.8.8" `
    -PassThru
```

## Replication

> [!NOTE]
> Something to describe what I'am doing.

```powershell
netsh 
```
