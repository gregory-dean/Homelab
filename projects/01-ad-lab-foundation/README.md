# Project 01 — Active Directory Lab Foundation

## Overview

This project establishes the core identity infrastructure for the homelab by deploying Active Directory and integrating a Windows workstation into the domain.

Active Directory is commonly used in enterprise environments for authentication, authorization, and system management.

---

## Objectives

• Deploy a Windows Server domain controller  
• Create an Active Directory domain  
• Join a Windows client workstation to the domain  
• Create test users and groups  
• Validate authentication and connectivity  

---

## Systems Used

| System | Role |
|------|------|
| Windows Server | Domain Controller |
| Windows Client | Domain Workstation |
| VirtualBox | Virtualization Platform |

---

## Build Steps

1 Deploy Windows Server VM

2 Configure static IP address

3 Install Active Directory Domain Services

4 Promote server to domain controller

5 Create domain users

6 Deploy Windows client VM

7 Join client to the domain

8 Verify login with domain credentials

---

## Validation

Successful deployment was confirmed through the following checks.

• Domain controller operational  
• Client joined to the domain  
• Domain user login successful  
• Systems communicate over the network  

---

## Screenshots

Screenshots documenting the setup process can be found in the screenshots directory.

---

## Skills Demonstrated

• Windows Server administration  
• Active Directory deployment  
• Domain management  
• Virtual machine networking  
• Security documentation  

---

## Next Steps

• Add Group Policy configuration  
• Integrate systems into SIEM logging  
• Generate authentication events for monitoring
