# Network settings

## Rename computer

> [!NOTE]
> Change adapter name to another so you will know whith which interface you want to work.

```ps
Rename-Computer -NewName "WIN-PS"
```

## Rename adapters

> [!NOTE]
> Change adapter name to another so you will know whith which interface you want to work.

```ps
Rename-NetAdapter -Name "Ethernet1" -NewName "Internal"
Rename-NetAdapter -Name "Ethernet2" -NewName "External"
```

## Set ip address

> [!NOTE]
> Set your interface to dhcp

```ps
netsh int ipv4 set add "External" source=dhcp
```

> [!NOTE]
> Set your interface to static ip address

```ps
netsh int ipv4 set add "Internal" static 10.10.10.1 255.255.255.0 10.10.10.254
```

## Set dns server

> [!NOTE]
> Set your DNS servers ip address, or the whatever you want to use.

```ps
netsh int ipv4 set dns "Internal" static 10.10.10.254
```
