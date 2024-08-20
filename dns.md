# DNS

## Install services

> [!NOTE]
> Install DNS service if you haven't already done it. You can use it in standalone mode and with adds. Adds uses DNS, so if you install it later, you will not have any conflicts with it.

```powershell
Install-WindowsFeature
    -name DNS
    -IncludeManagementTools
```

## Default settings

> [!NOTE]
> By deafult it listens on all existing ip addresses, but you can set on which interface to listen. You can check it by writingGet-DNSServerSetting -ALL` on administrator powershell. You can set by these commands: (With this you will just set IPv4 address, so add your IPv6 address if needed.)

```powershell
$DnsServerSettings = Get-DnsServerSetting -ALL
$DnsServerSettings.ListeningIpAddress = @("192.168.1.8")
Set-DNSServerSetting $DnsServerSettings
``

## Reverse zone IPv4

> [!NOTE]
> I recommend you to create first the reverse zones, because when you add A records for forward zone then you can create with one command the PTR, records too for that and it's easier, but if you need I'll provide add PTR record for just IPv4, because IPv6 is really hard for this. Create primary zone, if you don't have already one on another server. If you do, then go to Stub zone and replication. (IP -> Hostname).

```powershell
Add-DnsServerPrimaryZone -NetworkId "192.168.19.0/24" -ZoneFile "19.168.192.in-addr.arpa.dns"
```

> [!NOTE]
> You can add records into your zone like this.

```powershell
# Add an PTR record
Add-DnsServerResourceRecordPtr
    -ZoneName "19.168.192.in-addr.arpa"
    -Name "200"
    -PtrDomainName "www.tg.home"
```

## Reverse zone IPv6

> [!NOTE]
> For this IPv6 prefix2001:db8:0323::/48` you have to use this zone:

```powershell
Add-DnsServerPrimaryZone -NetworkId "2001:db8:0323::/48" -ZoneFile "3.2.3.0.8.b.d.0.1.0.0.2.in-addr.arpa.dns"
```

## Forward zone

> [!NOTE]
> Create primary zone, if you don't have already one on another server. If you do, then go to Stub zone and replication. (Hostname -> IP)

```powershell
Add-DnsServerPrimaryZone
    -Name "tg.home"
    -ZoneFile "tg.home.dns"
    -DynamicUpdate None
```

> [!NOTE]
> You can add records into your zone like this.

```powershell
# Add an A record
Add-DnsServerResourceRecordA
    -ZoneName "tg.home"
    -Name "www"
    -IPv4Address "192.168.1.200"
    -CreatePtr $true

# Add an AAAA record
Add-DnsServerResourceRecordAAAA
    -ZoneName "tg.home"
    -Name "www"
    -IPv6Address "2001:db8:0323::f1a3"
    -CreatePtr $true

# Add a CNAME record
Add-DnsServerResourceRecordCName
    -ZoneName "tg.home"
    -Name "mail"
    -HostNameAlias "www.tg.home"
    -CreatePtr $true

# Add an MX record
Add-DnsServerResourceRecordMX
    -ZoneName "tg.home"
    -Name "@"
    -MailExchange "mail.example.com"
    -Preference 10
    -CreatePtr $true
```

## Stub zone

> [!NOTE]
> This is for creating a 'copy' to another server, reduce latency of the users queries and redundant. You will have the zone from the other DNS server. You just have to know the name of the zone and the ip address of the server.

```powershell
Add-DnsServerStubZone -Name "tg.home" -MasterServers "192.168.19.200" (-ReplicationScope "Forest") # Add this to the end if you're using adds on both device.
```

## Forwarder

> [!NOTE]
> Set up another ip forwarder DNS server. (A server that will provide DNS lookup if yours don't know what the client looking for.)

```powershell
Add-DnsServerForwarder
    -IPAddress "8.8.8.8"
    -PassThru
```
