# 🖥️ Active Directory Lab Project

This project demonstrates a basic Active Directory (AD) environment using Oracle VirtualBox with Windows Server 2019 and Windows 10 Pro VMs. It covers domain setup, DHCP configuration, remote access, and user provisioning via PowerShell.

---

## 📌 Overview

The goal of this lab was to simulate a small enterprise AD environment for learning and demonstration purposes. It includes:

- Domain Controller setup with AD DS and DHCP
- Domain join and validation using a client VM
- PowerShell scripting to automate user creation
- Remote Desktop testing for admin connectivity

---

## 🛠️ Lab Environment

| Component     | Details                         |
|---------------|----------------------------------|
| Hypervisor    | Oracle VirtualBox               |
| Domain Controller (DC) | Windows Server 2019 (VM)  |
| Client Machine (CLIENT1) | Windows 10 Pro (VM)       |

---

## 🔧 Key Configurations

- Installed **Active Directory Domain Services (AD DS)** on the DC VM
- Created **Admin** and **User** Organizational Units (OUs)
- Configured **DHCP Server** to assign IPs to domain-joined devices
- Enabled **Remote Desktop Access** on the DC
- Joined CLIENT1 VM to the domain to verify connectivity and DHCP

---

## ⚙️ Automation with PowerShell

Used a PowerShell script to bulk-create **1,000 users** in the **Users OU**.

📁 Script:

```powershell
# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"
$USER_FIRST_LAST_LIST = Get-Content .\names.txt
# ------------------------------------------------------ #

$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force
New-ADOrganizationalUnit -Name _USERS -ProtectedFromAccidentalDeletion $false

foreach ($n in $USER_FIRST_LAST_LIST) {
    $first = $n.Split(" ")[0].ToLower()
    $last = $n.Split(" ")[1].ToLower()
    $username = "$($first.Substring(0,1))$($last)".ToLower()
    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $first `
               -Surname $last `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_USERS,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
}

```
---
