# Ansible

> [!NOTE]
> There are some builtin modules for Windows in ansible, so I get all of these, what I will use in the future, into this repository.

## Pre-configuration

> [!IMPORTANT]
> It is important to allow traffic for winrm, you can allow unencrypted, or configure encrypted. The computer has to have an ip address too.

`unencrypted`

```powershell
Set-Item -Path WSMan:\localhost\Service\AllowUnencrypted -value true
```

> [!NOTE]
> You can run this command to check that on both port are your server listening.

```powershell
winrm enumerate winrm/config/Listener
```

> [!NOTE]
> If not, run this command (You have to has a certificate for that so I would rather use the other option, if your VM isn't joined into a domain.)

`encrypted`

```powershell
winrm quickconfig -transport:https
```

## Set up LOCAL Users and Groups

```yaml
---
- name: Set up users and groups
  hosts: DC
  become: true
  become_user: Administrator
  become_password: Passw0rd
  become_method: runas
  gather_facts: false
  tasks:
    - name: Create local group contain new users
      win_group:
        name: LocalGroup
    
    - name: Create local user
      win_user:
        name: '{{ item.name }}'
        password: '{{ item.password }}'
        groups: LocalGroup
        update_password: false
        password_never_expires: true
      loop:
        - name: User1
          password: Passw0rd
        - name: User1
          password: Passw0rd
```

## Set up DOMAIN Users and Groups

```yaml
---
- name: Set up users and groups
  hosts: DC
  become: true
  become_user: Administrator
  become_password: Passw0rd
  become_method: runas
  gather_facts: false
  tasks:
    - name: Create domain group contain new users
      win_domain_group:
        name: DomainGroup
    
    - name: Create domain user
      win_domain_user:
        name: '{{ item.name }}'
        password: '{{ item.password }}'
        groups: LocalGroup
        update_password: false
        password_never_expires: true
        company: Ansible
      loop:
        - name: User1
          password: Passw0rd
        - name: User1
          password: Passw0rd
```

## Working with directories

```yaml
---
- name: Working with directories
  hosts: DC
  become: true
  become_user: Administrator
  become_password: Passw0rd
  become_method: runas
  gather_facts: false
  tasks:
    - name: Create directory
      win_file:
        path: C:\Development
        type: directory
    
    - name: Share directory
      win_share:
        name: internal
        description: top secret share
        path: C:\shares\internal
        list: false
        full: Administrators,CEO
        read: HR-Global
        deny: HR-External

        
    - name: Add public company share
    ansible.windows.win_share:
        name: company
        description: top secret share
        path: C:\shares\company
        list: true
        full: Administrators,CEO
        read: Global

    - name: Remove previously added share
      ansible.windows.win_share:
        name: internal
        state: absent

    - name: Set ACL for Development folder
      win_acl:
        path: C:\Development
        rights: FullControl
        state: present
        type: allow
        user: LocalGroup

    - name: Remove parent inheritance of Development folder
      win_acl_inheritance:
        path: C:\Development
        reorganize: true
        state: absent
```

## Creating domains

```yaml
---
- name: Creating ws2024.fr
  hosts: CL1
  gather_facts: false
  tasks:
  - name: Creating domain
    win_domain:
      safe_mode_password: Passw0rd!
      dns_domain_name: ws2024.fr
    register: domain
  - name: Reboot
    win_reboot:
    when: domain.reboot_required
  - name: Promoting to DC
    win_domain_controller:
      dns_domain_name: ws2024.fr
      domain_admin_user: WS2024\Administrator
      domain_admin_password: Passw0rd!
      state: domain_controller
      safe_mode_password: Passw0rd!
    register: dc
  - name: Reboot
    win_reboot:
    when: dc.reboot_required

- name: Joining CL2 to domain
  hosts: CL2
  gather_facts: false
  tasks:
  - name: Joining domain
    win_domain_membership:
      domain_admin_user: WS2024\Administrator
      domain_admin_password: Passw0rd!
      state: domain
      dns_domain_name: ws2024.fr
    register: domain
  - name: Reboot
    win_reboot:
    when: domain.reboot_required

- name: Promoting CL2 to RODC
  hosts: CL2
  become: True
  become_method: runas
  vars:
    ansible_become_user: WS2024\Administrator
    ansible_become_password: Passw0rd!
  gather_facts: false
  tasks:
  - name: Installing ADDS
    win_feature:
      name: AD-Domain-Services
      include_management_tools: True
      include_all_subfeatures: True
  - name: Promoting to RODC
    win_domain_controller:
      dns_domain_name: ws2024.fr
      domain_admin_user: WS2024\Administrator
      domain_admin_password: Passw0rd!
      state: domain_controller
      safe_mode_password: Passw0rd!
      read_only: True
      site_name: Default-First-Site-Name
    register: rodc
  - name: Reboot
    win_reboot:
    when: rodc.reboot_required

- name: Joining DL servers to domain
  hosts: DL1, DL2
  gather_facts: false
  tasks:
  - name: Joining domain
    win_domain_membership:
      domain_admin_user: WS2024\Administrator
      domain_admin_password: Passw0rd!
      state: domain
      dns_domain_name: ws2024.fr
    register: domain
  - name: Reboot
    win_reboot:
    when: domain.reboot_required
    
- name: Joining PA-RTR to domain
  hosts: PA-RTR
  gather_facts: false
  tasks:
  - name: Joining domain
    win_domain_membership:
      domain_admin_user: WS2024\Administrator
      domain_admin_password: Passw0rd!
      state: domain
      dns_domain_name: ws2024.fr
    register: domain
  - name: Reboot
    win_reboot:
    when: domain.reboot_required
```

## Using shell

```yaml
---
- name: Using shell
  hosts: DC
  become: true
  become_user: Administrator
  become_password: Passw0rd
  become_method: runas
  gather_facts: false
  tasks:
    - name: Run a powershell command
      win_shell: ls

    - name: Run a powershell command in mulitple line
      win_shell: > 
      ls
      -la

    - name: Run multiple powershell commands
      win_shell: |
      ls
      clear

    - name: Run a  cmd command
      win_command: cmd.exe /c mkdir C:\temp
```


