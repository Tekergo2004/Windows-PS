# ADDS USERS

## Create organizational units (ous)

> [!NOTE]
> It is essential to create organizationak units and assign every object into them later.

```powershell
New-ADOrganizationalUnit -Name "IT" -Path "DC=TG,DC=NET" -ProtectedFromAccidentalDeletion $False
New-ADOrganizationalUnit -Name "Cisco" -Path "OU=IT,DC=TG,DC=NET" -ProtectedFromAccidentalDeletion $False

# OU=IT,DC=TG,DC=NET = TG.NET/IT  --- For creating more objects 
```

## Create groups

> [!NOTE]
> There's more categorios that you can create in a ADDS system. You can set Group category to `Distribution` or `Security` (it's security by default, if you do it over GUI). You can set Group scope too, `DomainLocal`, `Global` or `Universal`.

```powershell
New-ADGroup -Name "Sysadmin" -SamAccountName Sysadmin -GroupCategory Security -GroupScope Global -DisplayName "Sysadmin" -Path "CN=IT,DC=TG,DC=NET" -Description "This is for system administrators in our corporate." 
```

## Create users

> [!NOTE]
> If you create an ADDS for your organization you will need users for your employees, what you can assign groups and organizational units. When you creating users you will likely to use templates for that and give them password and assign every paramaters to that object you're creating. (There is a lot so check out over this <a href="https://learn.microsoft.com/en-us/powershell/module/activedirectory/new-aduser?view=windowsserver2022-ps" target="_blank">link</a>) 

```powershell
# Create normal user (minimal settings)
New-ADUser -Name 'Falu' -AccountPassword (Read-Host -AsSecureString 'Password') -Enabled $true

# More useful parameters (recommend)
New-ADUser -Name 'Sysadmin1' -AccountPassword (Read-Host -AsSecureString 'Password') -CannotChangePassword ($false/$true) -PasswordNeverExpires ($false/$true) -City 'gyor' -company 'jedlik' -DisplayName 'System Administrator1' -GivenName 'System Administrator1' -EmailAddress 'sysadmin1@tg.net' -HomeDirectory '\\TG.NET\%USERNAME%' -HomeDrive 'H:'(company drive)  -Enabled ($false/$true) -Path "CN=Sysadmin,OU=IT,DC=TG,DC=NET" -SamAccountName 'System Administrator1'
```

## Join to group

> [!NOTE]
> This is how you have to add users to a group using powershell.

```powershell
Add-ADGroupMember -Identity "Sysadmin" -Members Falu
```

## Add computers

> [!NOTE]
> Add computer to a domain (on the computer u want to add to the domain).

```powershell
$Password = ConvertTo-SecureString "Password" -AsPlainText -Force
$Credential = New-Object System.Management.Automation.PSCredential("TG\Administrator", $Password)
$LocalCredential = New-Object System.Management.Automation.PSCredential("POWERSHELL\Administrator", $Password)


Add-Computer -ComputerName POWERSHELL -OUPath "OU=IT,DC=TG,DC=NET" -LocalCredential $LocalCredential -DomainName TG.NET -Credential $Credential -Restart -Force
```
