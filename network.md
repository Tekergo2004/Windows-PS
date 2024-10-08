# Network settings

## Rename computer

> [!NOTE]
> Change adapter name to another so you will know whith which interface you want to work.

```powershell
Rename-Computer -NewName "WIN-powershell"
```

## Rename adapters

> [!NOTE]
> Change adapter name to another so you will know whith which interface you want to work.

```powershell
Rename-NetAdapter -Name "Ethernet1" -NewName "Internal"
Rename-NetAdapter -Name "Ethernet2" -NewName "External"
```

## Set ip address

> [!NOTE]
> Set your interface to dhcp

```powershell
netsh int ipv4 set add "External" source=dhcp
```

> [!NOTE]
> Set your interface to static ip address

```powershell
netsh int ipv4 set add "Internal" static 192.168.19.1 255.255.255.0 10.10.10.254
```

```powershell
netsh int ipv6 set add "External" 2001:db8:f1a3::1/48
```

> [!NOTE]
> Set IPv6 gateway

```powershell
netsh interface ipv6 add route ::/0 "External" 2001:db8:f1a3::1
```

## Set dns server

> [!NOTE]
> Set your DNS servers ip address, or the whatever you want to use.

```powershell
netsh int ipv4 set dns "Internal" static 10.10.10.254
```
