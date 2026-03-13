# Cybersecurity Homelab

This repository documents the design, build process, and experiments performed in my personal cybersecurity homelab.

The purpose of this project is to create a controlled environment where I can simulate real-world enterprise security scenarios including network defense, attack simulation, vulnerability management, and threat detection.

The lab is continuously evolving as I expand the infrastructure and add new security tools and testing scenarios.

---

## Objectives

• Build a realistic enterprise-style security lab  
• Practice both offensive and defensive security techniques  
• Simulate attack scenarios and investigate the resulting logs  
• Develop experience with SIEM tools and detection engineering  
• Document the process publicly for learning and knowledge sharing  

---

## Lab Architecture

The homelab is built using a Windows 11 host machine running multiple virtual machines in VirtualBox. These systems simulate an enterprise network environment with domain services, user workstations, monitoring infrastructure, and attack systems.

Current core components include:

• Windows Server (Active Directory Domain Controller)  
• Windows Client workstation  
• Kali Linux attack machine  
• Ubuntu Server for hosting vulnerable services  
• SIEM platform for log aggregation and analysis  

Additional infrastructure such as firewalls, intrusion detection systems, and vulnerability scanners will be added in later phases.

---

## Project Roadmap

### Phase 1 – Foundation

Establish the base virtual environment.

Tasks:

• Configure VirtualBox networking  
• Deploy core virtual machines  
• Configure Active Directory domain  
• Establish communication between systems  

---

### Phase 2 – Security Monitoring

Introduce log aggregation and threat detection.

Tasks:

• Deploy SIEM platform (Splunk / ELK)  
• Configure log ingestion from Windows and Linux systems  
• Create detection rules and alerts  
• Map detections to MITRE ATT&CK techniques  

---

### Phase 3 – Vulnerability Management

Simulate enterprise vulnerability management workflows.

Tasks:

• Deploy vulnerability scanner  
• Run authenticated and unauthenticated scans  
• Analyze findings and risk levels  
• Perform remediation and verify fixes  

---

### Phase 4 – Attack Simulation

Simulate real-world attacker behavior within the environment.

Activities include:

• Network reconnaissance  
• Credential attacks  
• Privilege escalation  
• Lateral movement  
• Data exfiltration  

All attack activity will be correlated with detection and investigation in the monitoring platform.

---

## Tools and Technologies

Operating Systems

• Windows Server  
• Windows 10 / 11  
• Ubuntu Server  
• Kali Linux  

Security Tools

• Nmap  
• Metasploit  
• Burp Suite  
• Splunk / Elastic Stack  
• Wireshark  
• Nessus / OpenVAS  

Infrastructure

• VirtualBox  
• Active Directory  
• Linux networking  

---

## Documentation

Detailed documentation for each stage of the homelab build can be found in the repository directories.

Each section includes:

• Configuration steps  
• Screenshots  
• Command outputs  
• Lessons learned  

---

## Purpose of This Project

This homelab is designed as a long-term learning environment to deepen my understanding of cybersecurity concepts and real-world defensive and offensive techniques.

The project also serves as a public portfolio documenting my work as I continue developing my skills in cybersecurity.

---

## Author

Gregory Dean  
Cybersecurity Analyst | Security Research | Homelab Development
