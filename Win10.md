# Win 10

## Debloating Windows 10

> [!NOTE]
> Ignore the warnings. Run in powershell administrator. Recommend for new windows VM-s, if you use them for testing or etc.

```powershell
Get-AppxPackage | Remove-AppxPackage
```