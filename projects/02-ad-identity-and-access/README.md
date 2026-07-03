# Project 02 – AD Identity and Access

## Overview

This project documents the Identity and Access phase of my cybersecurity homelab build. The goal of this phase was to transform the Windows Server system into a functioning domain controller, establish a basic Active Directory structure, create test identities, join the Windows client to the domain, and validate authentication across the lab.

This phase builds directly on the networking and VM deployment work completed in Project 01 and establishes the identity foundation required for future logging, monitoring, policy enforcement, and attack simulation work.

---

## Objectives

- Install Active Directory Domain Services on `DC-01`
- Promote `DC-01` to a domain controller
- Configure the lab domain and internal DNS
- Create organizational units, users, and groups
- Join `WINCLIENT-01` to the domain
- Validate domain authentication and policy application
- Document the build in a clean and repeatable way

---

## Environment Summary

### Host system
- Windows 11 desktop
- Oracle VirtualBox

### Virtual machines used in this phase
- `DC-01` – Windows Server 2022
- `WINCLIENT-01` – Windows 11
- `KALI-01` – Kali Linux
- `UBUNTU-01` – Ubuntu Server

### Domain configuration
- Domain: `lab.lan`
- NetBIOS: `LAB`
- Internal subnet: `192.168.56.0/24`

---

## Starting State

At the start of this phase, all four core virtual machines were already deployed and connected to the internal lab network from Project 01.

`DC-01` was prepared to become the first domain controller, while `WINCLIENT-01` was positioned to become the first domain-joined workstation.

![Project 01 completed state](../01-lab-foundation-and-networking/images/31-virtualbox-all-machines-running.png)

---

## DC-01 Preparation

Before installing Active Directory, I verified the Windows Server configuration, hostname, IP addressing, and internal network settings.

### IP configuration verification
![DC-01 ipconfig](images/00-dc-01-ipconfig-all.png)

### Windows Update check
![DC-01 Windows Update](images/01-dc-01-dc-01-windows-update.png)

### Adapter naming for clarity
![DC-01 adapter naming](images/02-dc-01-ncpa-cpl-nat-lab.png)

---

## Active Directory Domain Services Installation

The first major task in this phase was installing the Active Directory Domain Services role on `DC-01`.

### Add Roles and Features wizard
![Add Roles and Features](images/03-dc-01-add-roles-and-features.png)

### Role-based installation selection
![Role-based installation](images/04-dc-01-role-based-intall.png)

### Destination server selection
![Destination server selection](images/05-dc-01-select-destination-server.png)

### Active Directory Domain Services role selected
![AD DS selected](images/06-dc-01-active-directory-domain-services.png)

### AD DS confirmation
![AD DS confirmation](images/07-dc-01-ad-ds.png)

### Confirm installation selections
![Confirm installation selections](images/08-dc-01-confirm-installation-selections.png)

### Installation succeeded
![Installation succeeded](images/09-dc-01-install-succeeded.png)

---

## Domain Controller Promotion

After the AD DS role was installed, `DC-01` was promoted as the first domain controller in a new forest.

### Promote this server to a domain controller
![Promote domain controller](images/10-dc-01-promote-server-domain-controller.png)

### New forest creation
![New forest](images/11-dc-01-add-new-forest.png)

### DSRM password and DNS prompt
![Password and DNS prompt](images/12-dc-01-set-password-dns-prompt.png)

### NetBIOS domain name
![NetBIOS domain name](images/13-dc-01-netbios-domain-name.png)

### Prerequisites check
![Prerequisites check](images/14-dc-01-prerequisites-check-install.png)

### Reboot/sign-out notice
![Signed out notice](images/15-dc-01-you-are-about-to-be-signed-out.png)

### Post-reboot domain administrator login
![Domain administrator login](images/16-dc-01-lab-administator-login.png)

---

## Domain and DNS Validation

Once the server was promoted, I verified that Active Directory and DNS were both functioning correctly.

### Active Directory Users and Computers
![ADUC domain view](images/17-dc-01-active-directory-users-and-computers.png)

### DNS zone verification
![DNS zone verification](images/18-dc-01-verify-dns-zone.png)

### DNS forwarder configuration
![DNS forwarder](images/19-dc-01-set-dns-forwarder.png)

### LAB adapter IPv4 properties
![LAB adapter IPv4 properties](images/20-dc-01-lab-adapter-ipv4-properties.png)

### DNS registration refresh
![Flush and register DNS](images/21-dc-01-flushdns-registerdns.png)

### Post-DNS adjustment validation
![Post-DNS adjustment ipconfig](images/22-dc-01-ipconfigall-after-dns-adjustment.png)

---

## Organizational Unit Design

To keep the environment clean and prepare for future policy targeting, I created a simple OU structure for the lab.

### OU creation
![OU creation dialog](images/23-dc-01-ou-creation-dialog.png)

### OU structure in ADUC
![OU structure](images/24-dc-01-ad-tree-showing-ous.png)

---

## Users and Groups

The next step was creating lab identities and security groups for basic administration and workstation access.

### Global security groups
![Global security groups](images/25-dc-01-create-global-security-groups.png)

### All global security groups
![All global security groups](images/26-dc-01-all-global-security-groups.png)

### Standard user creation
![Create greg.dean](images/27-dc-01-new-object-user-greg-dean.png)

### Separate administrative account
![Create separate admin account](images/28-dc-01-create-separate-admin-account.png)

### Group membership assignment
![Assign group membership](images/29-dc-01-assign-group-membership.png)

### greg.dean properties
![greg.dean properties](images/30-dc-01-properties-greg-dean.png)

### helpdesk.test properties
![helpdesk.test properties](images/31-dc-01-properties-help-desk.png)

### labadmin properties
![labadmin properties](images/32-dc-01-properties-labadmin.png)

---

## Group Policy Basics

This phase also included documenting basic Group Policy configuration for account and password controls.

### Group Policy console
![Group Policy console](images/33-dc-01-group-policy-console.png)

### Password policy
![Password policy](images/34-dc-01-group-policy-management-editor-password-policy.png)

### Account lockout policy
![Account lockout policy](images/35-dc-01-configure-account-lockout-policy.png)

### Domain firewall profile enabled
![Domain firewall profile](images/36-dc-01-firewall-state-enabled-domain-profile.png)

### Workstation-linked GPO
![Workstation-linked GPO](images/37-dc-01-phase2-workstation-linked-workstations.png)

---

## WINCLIENT-01 Domain Join

After Active Directory was ready, I prepared the Windows client by confirming its hostname, static internal IP, and DNS settings, then joined it to the lab domain.

### Hostname verification
![WINCLIENT-01 hostname](images/38-winclient-01-hostname.png)

### Adapter rename
![WINCLIENT-01 adapter rename](images/39-winclient-01-rename-adapters.png)

### IPv4 and DNS configuration
![WINCLIENT-01 IPv4 DNS](images/40-winclient-01-ipv4-dns.png)

### Domain resolution test
![WINCLIENT-01 nslookup and ping](images/41-winclient-01-nslookup-ping.png)

### Join operation
![WINCLIENT-01 join domain](images/42-winclient-01-join-domain.png)

### Welcome to domain
![WINCLIENT-01 welcome to domain](images/43-winclient-01-welcome-to-domain.png)

### Domain sign-in option
![WINCLIENT-01 domain sign-in](images/44-winclient-01-sign-in-showing-domain.png)

### Client moved into Workstations OU
![WINCLIENT-01 moved to Workstations](images/45-winclient-01-aduc-client-moved-workstations.png)

---

## Domain Authentication Validation

After the reboot, I logged in with a domain user account and confirmed that authentication and policy processing were working correctly.

### Domain user login
![gregdean domain login](images/46-winclient-01-gregdean-domain-login.png)

### Identity validation
![Identity validation](images/47-winclient-01-validate-identity.png)

### Group Policy result
![gpresult](images/48-winclient-01-gpresult.png)

### Helpdesk lockout test
![Helpdesk lockout test](images/49-winclient-01-helpdesk-lockout-test.png)

---

## Cross-System Validation

`KALI-01` and `UBUNTU-01` were intentionally kept off-domain during this phase. They were used only to validate internal DNS resolution and network consistency from the Linux side of the lab.

### KALI-01 nslookup validation
![KALI-01 nslookup](images/50-kali-01-nslookup-winclient-dc.png)

### KALI-01 network state
![KALI-01 ip a](images/51-kali-01-ip-a.png)

### KALI-01 routes
![KALI-01 ip route](images/52-kali-01-ip-route.png)

### KALI-01 ping
![KALI-01 ping](images/53-kali-01-ping.png)

### UBUNTU-01 dig validation
![UBUNTU-01 dig](images/54-ubuntu-01-dig.png)

### UBUNTU-01 network state
![UBUNTU-01 ip a and ip route](images/55-ubuntu-01-ip-a-ip-route.png)

### UBUNTU-01 ping
![UBUNTU-01 ping](images/56-ubuntu-01-ping-results.png)

---

## Key Outcomes

- Converted `DC-01` into a functioning domain controller
- Established the `lab.lan` lab domain
- Configured internal DNS to support Active Directory
- Created a basic OU structure for systems, users, and administration
- Added test users, security groups, and a dedicated admin account
- Joined `WINCLIENT-01` to the domain successfully
- Validated domain authentication and Group Policy processing
- Preserved `KALI-01` and `UBUNTU-01` as non-domain systems for future phases

---

## Notes

This phase focused only on identity and access management. It did not include SIEM deployment, log forwarding, attack simulation, Linux domain integration, or advanced hardening. Those tasks are planned for later projects.

I captured additional screenshots throughout the build to preserve validation evidence, system state, and troubleshooting history. The full image set is stored under `images/` and a curated subset is used throughout this README to document the project flow.

---

## Next Phase

The next phase will focus on Monitoring and Detection by deploying a SIEM platform, onboarding Windows and Linux logs, validating ingestion, and building the first detection workflows.

---
