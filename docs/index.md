# Cybersecurity Homelab Projects

![Homelab Banner](https://img.shields.io/badge/Homelab-Cybersecurity-blue?style=for-the-badge&logo=security&logoColor=white) ![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=for-the-badge&logo=arch-linux&logoColor=white) ![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white) ![PostgreSQL](https://img.shields.io/badge/postgresql-4169e1?style=for-the-badge&logo=postgresql&logoColor=white)

Welcome to my cybersecurity homelab documentation. This site showcases two major infrastructure projects I've built to demonstrate my skills in network security, virtualization, and self-hosted solutions.

## Project Overview

### [Homelab & Networking Setup](homelab)

![Completed](https://img.shields.io/badge/Status-Completed-success)

I built a comprehensive homelab environment using Proxmox VE for virtualization, implementing secure file sharing with Samba, and configuring proper network security practices. The setup includes multiple Arch Linux VMs with custom configurations and secure remote access.

**Key Technologies:** Proxmox VE, Arch Linux, Samba, SSH, LVM, NTFS-3G

---

### [Self-Hosted Supabase with Secure Access](supabase)

![In Production](https://img.shields.io/badge/Status-In_Production-red)

I deployed a containerized Supabase instance on Arch Linux with Docker, implementing secure remote access through Twingate's zero-trust network access. The setup provides a fully functional PostgreSQL-based backend accessible securely from anywhere.

**Key Technologies:** Docker, Supabase, PostgreSQL, Twingate, Arch Linux

---

## Environment Setup

All projects were built and tested in the following environment:

- **Host OS**: Proxmox VE 8.x
- **Guest OS**: Arch Linux (latest)
- **Virtualization**: KVM/QEMU
- **Storage**: LVM with ext4 filesystem
- **Networking**: Bridged networking with DHCP

## Documentation Features

This documentation includes:

- **Step-by-step guides** with explanations for each command
- **Copy-paste reproduction blocks** for easy replication
- **Troubleshooting sections** for common issues
- **Verification commands** to test each setup
- **Security considerations** and best practices

