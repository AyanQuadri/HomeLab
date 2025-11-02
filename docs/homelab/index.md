# Homelab & Networking Setup

![Arch Linux](https://img.shields.io/badge/Arch_Linux-1793D1?style=flat&logo=arch-linux&logoColor=white) ![Proxmox](https://img.shields.io/badge/Proxmox-E57000?style=flat&logo=proxmox&logoColor=white) ![Samba](https://img.shields.io/badge/Samba-CC3A3B?style=flat&logo=samba&logoColor=white) ![Status](https://img.shields.io/badge/Status-Completed-success)

## One-Minute Summary

I built a comprehensive cybersecurity homelab environment featuring:
• **Proxmox VE virtualization platform** with optimized storage management and enterprise repository configuration
• **Multiple Arch Linux VMs** with custom shell environments, development tools, and security hardening
• **Secure file sharing system** using Samba with NTFS drive integration and proper access controls
• **Network security implementation** with SSH hardening and user privilege management

---

## Project Timeline

<div class="timeline">
  <div class="timeline-item">
    <div class="timeline-marker"></div>
    <div class="timeline-content">
      <h4>Proxmox Setup</h4>
      <p>Installed Proxmox VE, configured storage, disabled enterprise repos</p>
    </div>
  </div>
  <div class="timeline-item">
    <div class="timeline-marker"></div>
    <div class="timeline-content">
      <h4>Arch Linux Deployment</h4>
      <p>Deployed and configured multiple Arch Linux VMs with custom environments</p>
    </div>
  </div>
  <div class="timeline-item">
    <div class="timeline-marker"></div>
    <div class="timeline-content">
      <h4>File Sharing & Security</h4>
      <p>Implemented Samba file sharing with NTFS integration and SSH security</p>
    </div>
  </div>
  <div class="timeline-item">
    <div class="timeline-marker"></div>
    <div class="timeline-content">
      <h4>Testing & Documentation</h4>
      <p>Comprehensive testing, performance optimization, and documentation</p>
    </div>
  </div>
</div>

---

## Copy-Paste Reproduction

Here's the minimal command sequence to reproduce this setup:

```bash
# 1. Configure Proxmox repositories (on Proxmox host)
sudo nano /etc/apt/sources.list.d/pve-enterprise.list  # Comment out enterprise repo
echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" | sudo tee /etc/apt/sources.list.d/pve-no-subscription.list
sudo apt update && sudo apt upgrade -y

# 2. Setup file sharing (on Proxmox host)
apt update && apt install -y ntfs-3g samba openssh-server
mkdir -p /mnt/ntfs1 /mnt/ntfs2
mount -t ntfs-3g /dev/<your-disk1> /mnt/ntfs1
mount -t ntfs-3g /dev/<your-disk2> /mnt/ntfs2

# 3. Configure Samba and SSH security
adduser <your-username>
smbpasswd -a <your-username>
sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
systemctl restart ssh smbd nmbd
```
