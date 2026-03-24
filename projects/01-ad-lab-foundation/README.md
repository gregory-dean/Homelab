# Project 01 – AD Lab Foundation

## Overview

This project documents the Identity and Access phase of my cybersecurity homelab build. The goal of this phase was to transform the Windows Server system into a functioning domain controller, establish a basic Active Directory structure, create test identities, join the Windows client to the domain, and validate authentication across the lab.

This phase builds directly on the networking and VM deployment work completed in Project 00 and establishes the identity foundation required for future logging, monitoring, policy enforcement, and attack simulation work.

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

At the start of this phase, all four core virtual machines were already deployed and connected to the internal lab network from Project 00.

`DC-01` was prepared to become the first domain controller, while `WINCLIENT-01` was positioned to become the first domain-joined workstation.

![Project 00 completed state](../../assets/images/phase-1-foundation/31-virtualbox-all-machines-running.png)

---

## DC-01 Preparation

Before installing Active Directory, I verified the Windows Server configuration, hostname, IP addressing, and internal network settings.

### IP configuration verification
![DC-01 ipconfig](../../assets/images/phase-2-identity-and-access/00-dc-01-ipconfig-all.png)

### Windows Update check
![DC-01 Windows Update](../../assets/images/phase-2-identity-and-access/01-dc-01-dc-01-windows-update.png)

### Adapter naming for clarity
![DC-01 adapter naming](../../assets/images/phase-2-identity-and-access/02-dc-01-ncpa-cpl-nat-lab.png)

---

## Active Directory Domain Services Installation

The first major task in this phase was installing the Active Directory Domain Services role on `DC-01`.

### Add Roles and Features wizard
![Add Roles and Features](../../assets/images/phase-2-identity-and-access/03-dc-01-add-roles-and-features.png)

### Role-based installation selection
![Role-based installation](../../assets/images/phase-2-identity-and-access/04-dc-01-role-based-intall.png)

### Destination server selection
![Destination server selection](../../assets/images/phase-2-identity-and-access/05-dc-01-select-destination-server.png)

### Active Directory Domain Services role selected
![AD DS selected](../../assets/images/phase-2-identity-and-access/06-dc-01-active-directory-domain-services.png)

### AD DS confirmation
![AD DS confirmation](../../assets/images/phase-2-identity-and-access/07-dc-01-ad-ds.png)

### Confirm installation selections
![Confirm installation selections](../../assets/images/phase-2-identity-and-access/08-dc-01-confirm-installation-selections.png)

### Installation succeeded
![Installation succeeded](../../assets/images/phase-2-identity-and-access/09-dc-01-install-succeeded.png)

---

## Domain Controller Promotion

After the AD DS role was installed, `DC-01` was promoted as the first domain controller in a new forest.

### Promote this server to a domain controller
![Promote domain controller](../../assets/images/phase-2-identity-and-access/10-dc-01-promote-server-domain-controller.png)

### New forest creation
![New forest](../../assets/images/phase-2-identity-and-access/11-dc-01-add-new-forest.png)

### DSRM password and DNS prompt
![Password and DNS prompt](../../assets/images/phase-2-identity-and-access/12-dc-01-set-password-dns-prompt.png)

### NetBIOS domain name
![NetBIOS domain name](../../assets/images/phase-2-identity-and-access/13-dc-01-netbios-domain-name.png)

### Prerequisites check
![Prerequisites check](../../assets/images/phase-2-identity-and-access/14-dc-01-prerequisites-check-install.png)

### Reboot/sign-out notice
![Signed out notice](../../assets/images/phase-2-identity-and-access/15-dc-01-you-are-about-to-be-signed-out.png)

### Post-reboot domain administrator login
![Domain administrator login](../../assets/images/phase-2-identity-and-access/16-dc-01-lab-administator-login.png)

---

## Domain and DNS Validation

Once the server was promoted, I verified that Active Directory and DNS were both functioning correctly.

### Active Directory Users and Computers
![ADUC domain view](../../assets/images/phase-2-identity-and-access/17-dc-01-active-directory-users-and-computers.png)

### DNS zone verification
![DNS zone verification](../../assets/images/phase-2-identity-and-access/18-dc-01-verify-dns-zone.png)

### DNS forwarder configuration
![DNS forwarder](../../assets/images/phase-2-identity-and-access/19-dc-01-set-dns-forwarder.png)

### LAB adapter IPv4 properties
![LAB adapter IPv4 properties](../../assets/images/phase-2-identity-and-access/20-dc-01-lab-adapter-ipv4-properties.png)

### DNS registration refresh
![Flush and register DNS](../../assets/images/phase-2-identity-and-access/21-dc-01-flushdns-registerdns.png)

### Post-DNS adjustment validation
![Post-DNS adjustment ipconfig](../../assets/images/phase-2-identity-and-access/22-dc-01-ipconfigall-after-dns-adjustment.png)

---

## Organizational Unit Design

To keep the environment clean and prepare for future policy targeting, I created a simple OU structure for the lab.

### OU creation
![OU creation dialog](../../assets/images/phase-2-identity-and-access/23-dc-01-ou-creation-dialog.png)

### OU structure in ADUC
![OU structure](../../assets/images/phase-2-identity-and-access/24-dc-01-ad-tree-showing-ous.png)

---

## Users and Groups

The next step was creating lab identities and security groups for basic administration and workstation access.

### Global security groups
![Global security groups](../../assets/images/phase-2-identity-and-access/25-dc-01-create-global-security-groups.png)

### All global security groups
![All global security groups](../../assets/images/phase-2-identity-and-access/26-dc-01-all-global-security-groups.png)

### Standard user creation
![Create greg.dean](../../assets/images/phase-2-identity-and-access/27-dc-01-new-object-user-greg-dean.png)

### Separate administrative account
![Create separate admin account](../../assets/images/phase-2-identity-and-access/28-dc-01-create-separate-admin-account.png)

### Group membership assignment
![Assign group membership](../../assets/images/phase-2-identity-and-access/29-dc-01-assign-group-membership.png)

### greg.dean properties
![greg.dean properties](../../assets/images/phase-2-identity-and-access/30-dc-01-properties-greg-dean.png)

### helpdesk.test properties
![helpdesk.test properties](../../assets/images/phase-2-identity-and-access/31-dc-01-properties-help-desk.png)

### labadmin properties
![labadmin properties](../../assets/images/phase-2-identity-and-access/32-dc-01-properties-labadmin.png)

---

## Group Policy Basics

This phase also included documenting basic Group Policy configuration for account and password controls.

### Group Policy console
![Group Policy console](../../assets/images/phase-2-identity-and-access/33-dc-01-group-policy-console.png)

### Password policy
![Password policy](../../assets/images/phase-2-identity-and-access/34-dc-01-group-policy-management-editor-password-policy.png)

### Account lockout policy
![Account lockout policy](../../assets/images/phase-2-identity-and-access/35-dc-01-configure-account-lockout-policy.png)

### Domain firewall profile enabled
![Domain firewall profile](../../assets/images/phase-2-identity-and-access/36-dc-01-firewall-state-enabled-domain-profile.png)

### Workstation-linked GPO
![Workstation-linked GPO](../../assets/images/phase-2-identity-and-access/37-dc-01-phase2-workstation-linked-workstations.png)

---

## WINCLIENT-01 Domain Join

After Active Directory was ready, I prepared the Windows client by confirming its hostname, static internal IP, and DNS settings, then joined it to the lab domain.

### Hostname verification
![WINCLIENT-01 hostname](../../assets/images/phase-2-identity-and-access/38-winclient-01-hostname.png)

### Adapter rename
![WINCLIENT-01 adapter rename](../../assets/images/phase-2-identity-and-access/39-winclient-01-rename-adapters.png)

### IPv4 and DNS configuration
![WINCLIENT-01 IPv4 DNS](../../assets/images/phase-2-identity-and-access/40-winclient-01-ipv4-dns.png)

### Domain resolution test
![WINCLIENT-01 nslookup and ping](../../assets/images/phase-2-identity-and-access/41-winclient-01-nslookup-ping.png)

### Join operation
![WINCLIENT-01 join domain](../../assets/images/phase-2-identity-and-access/42-winclient-01-join-domain.png)

### Welcome to domain
![WINCLIENT-01 welcome to domain](../../assets/images/phase-2-identity-and-access/43-winclient-01-welcome-to-domain.png)

### Domain sign-in option
![WINCLIENT-01 domain sign-in](../../assets/images/phase-2-identity-and-access/44-winclient-01-sign-in-showing-domain.png)

### Client moved into Workstations OU
![WINCLIENT-01 moved to Workstations](../../assets/images/phase-2-identity-and-access/45-winclient-01-aduc-client-moved-workstations.png)

---

## Domain Authentication Validation

After the reboot, I logged in with a domain user account and confirmed that authentication and policy processing were working correctly.

### Domain user login
![gregdean domain login](../../assets/images/phase-2-identity-and-access/46-winclient-01-gregdean-domain-login.png)

### Identity validation
![Identity validation](../../assets/images/phase-2-identity-and-access/47-winclient-01-validate-identity.png)

### Group Policy result
![gpresult](../../assets/images/phase-2-identity-and-access/48-winclient-01-gpresult.png)

### Helpdesk lockout test
![Helpdesk lockout test](../../assets/images/phase-2-identity-and-access/49-winclient-01-helpdesk-lockout-test.png)

---

## Cross-System Validation

`KALI-01` and `UBUNTU-01` were intentionally kept off-domain during this phase. They were used only to validate internal DNS resolution and network consistency from the Linux side of the lab.

### KALI-01 nslookup validation
![KALI-01 nslookup](../../assets/images/phase-2-identity-and-access/50-kali-01-nslookup-winclient-dc.png)

### KALI-01 network state
![KALI-01 ip a](../../assets/images/phase-2-identity-and-access/51-kali-01-ip-a.png)

### KALI-01 routes
![KALI-01 ip route](../../assets/images/phase-2-identity-and-access/52-kali-01-ip-route.png)

### KALI-01 ping
![KALI-01 ping](../../assets/images/phase-2-identity-and-access/53-kali-01-ping.png)

### UBUNTU-01 dig validation
![UBUNTU-01 dig](../../assets/images/phase-2-identity-and-access/54-ubuntu-01-dig.png)

### UBUNTU-01 network state
![UBUNTU-01 ip a and ip route](../../assets/images/phase-2-identity-and-access/55-ubuntu-01-ip-a-ip-route.png)

### UBUNTU-01 ping
![UBUNTU-01 ping](../../assets/images/phase-2-identity-and-access/56-ubuntu-01-ping-results.png)

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

I captured additional screenshots throughout the build to preserve validation evidence, system state, and troubleshooting history. The full image set is stored under `assets/images/phase-2-identity-and-access/` and a curated subset is used throughout this README to document the project flow.

---

## Next Phase

The next phase will focus on Monitoring and Detection by deploying a SIEM platform, onboarding Windows and Linux logs, validating ingestion, and building the first detection workflows.

---
