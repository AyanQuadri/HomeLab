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

---

## Detailed Implementation Guide

## Phase 1: Proxmox Host Configuration

### 1.1 Repository Management

**Disable Enterprise Repository:**

```bash
sudo nano /etc/apt/sources.list.d/pve-enterprise.list
```

**What this does:** Opens the enterprise repository configuration file for editing  
**Why this matters:** Enterprise repos require a subscription and will cause update errors

Inside the file, comment out the enterprise repository line:

```bash
# deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise
```

**Add No-Subscription Repository:**

```bash
echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" | sudo tee /etc/apt/sources.list.d/pve-no-subscription.list
```

**What this does:** Adds the free Proxmox repository for updates  
**Why this matters:** Enables system updates without requiring a subscription  
**Gotcha:** Always check the Debian version (bookworm) matches your Proxmox version

### 1.2 System Updates

```bash
sudo apt update
```

**What this does:** Updates the package database with new repository information  
**Why this matters:** Ensures we have access to the latest packages

```bash
sudo apt upgrade -y
```

**What this does:** Upgrades all installed packages to their latest versions  
**Why this matters:** Applies security patches and bug fixes  
**Gotcha:** The `-y` flag automatically accepts all prompts

### 1.3 Storage Optimization

```bash
lvremove pve/data
```

**What this does:** Removes root user from Samba access  
**Why this matters:** Improves security by preventing root-level file share access

**Restart Samba Services:**

```bash
systemctl restart smbd nmbd
```

**What this does:** Restarts Samba file sharing services  
**Why this matters:** Applies configuration changes and enables shares

### 1.4 SSH Security Hardening

**Disable Root SSH Login:**

```bash
sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
```

**What this does:** Disables direct root login via SSH  
**Why this matters:** Critical security hardening to prevent root compromise  
**Gotcha:** Ensure you have a regular user with sudo access before doing this

**Restart SSH Service:**

```bash
systemctl restart ssh
```

**What this does:** Applies SSH configuration changes  
**Why this matters:** Makes the security changes active

---

## Verification Commands

Use these commands to verify each component is working correctly:

### System Health Check

```bash
# Check system resources
free -h
df -h
lsblk
```

### Network Services Status

```bash
# Check service status
systemctl status smbd
systemctl status nmbd
systemctl status ssh
```

### File Share Testing

```bash
# Test Samba shares
smbclient -L localhost -U <your-username>
```

### Network Connectivity

```bash
# Check listening ports
ss -tulpn | grep -E ':22|:139|:445'

# Test SSH access
ssh <your-username>@<server-ip>
```

### Storage Verification

```bash
# Check mounted filesystems
mount | grep ntfs
cat /etc/fstab | grep ntfs
```

---

## Troubleshooting Guide

??? question "Samba shares not visible from Windows"

    **Symptoms:** Cannot see shares in Windows Network
    **Solution:** Check firewall and enable SMB discovery
    ```bash
    # Check if Samba is listening
    ss -tulpn | grep :445

    # Restart Samba services
    systemctl restart smbd nmbd

    # Test local access
    smbclient -L localhost -U <username>
    ```

??? question "NTFS drives not mounting automatically"

    **Symptoms:** Drives not available after reboot
    **Solution:** Check fstab entries and mount manually
    ```bash
    # Check fstab entries
    cat /etc/fstab | grep ntfs

    # Test manual mount
    mount -a

    # Check for errors
    dmesg | grep ntfs
    ```

??? question "SSH connection refused"

    **Symptoms:** Cannot connect via SSH
    **Solution:** Verify SSH service and firewall
    ```bash
    # Check SSH service status
    systemctl status ssh

    # Verify SSH is listening
    ss -tulpn | grep :22

    # Check SSH configuration
    sshd -t
    ```

??? question "Proxmox updates failing"

    **Symptoms:** Package update errors
    **Solution:** Verify repository configuration
    ```bash
    # Check repository files
    ls /etc/apt/sources.list.d/

    # Update package lists
    apt update

    # Check for errors
    apt list --upgradable
    ```

??? question "Low storage space on Proxmox"

    **Symptoms:** Insufficient space for VMs
    **Solution:** Extend root logical volume
    ```bash
    # Check current space
    df -h /

    # Check LVM status
    pvs
    vgs
    lvs

    # Extend if space available
    lvextend -l +100%FREE pve/root
    resize2fs /dev/mapper/pve-root
    ```

??? question "Arch Linux VM won't boot"

    **Symptoms:** VM fails to start or boot
    **Solution:** Check GRUB and VM settings
    ```bash
    # From Proxmox console, check VM logs
    tail /var/log/pve/qemu-server/<vmid>.log

    # Boot from rescue media and check GRUB
    grub-mkconfig -o /boot/grub/grub.cfg
    ```

---


## Performance Metrics

| Component          | Configuration             | Performance                |
| ------------------ | ------------------------- | -------------------------- |
| **Proxmox Host**   | 8GB RAM, 4 CPU cores      | ~20% average load          |
| **Arch Linux VMs** | 2GB RAM, 2 CPU cores each | ~5% idle CPU usage         |
| **Storage**        | LVM on SSD                | ~150MB/s read/write        |
| **Network**        | Gigabit Ethernet          | Full bandwidth utilization |
| **Samba Shares**   | NTFS-3G mounted           | ~100MB/s file transfers    |

This homelab setup demonstrates practical cybersecurity infrastructure skills and provides a robust foundation for further security research and testing. the default 'data' logical volume  
**Why this matters:** Frees up space for the root filesystem to expand  
**Gotcha:** This is destructive - ensure you have backups if needed

```bash
lvextend -l +100%FREE pve/root
```

**What this does:** Extends the root logical volume to use all available free space  
**Why this matters:** Maximizes storage available for VMs and containers

```bash
resize2fs /dev/mapper/pve-root
```

**What this does:** Resizes the ext4 filesystem to use the newly extended space  
**Why this matters:** Makes the additional space actually usable by the system

**Verification:**

```bash
df -h
lsblk
```

## Phase 2: Arch Linux VM Setup

### 2.1 Initial Installation

**Start Arch Installation:**

```bash
pacman -Sy archinstall
```

**What this does:** Updates package database and installs the guided Arch installer  
**Why this matters:** Provides a user-friendly installation method

```bash
archinstall
```

**What this does:** Launches the guided Arch Linux installation wizard  
**Why this matters:** Simplifies the traditionally complex Arch installation process

### 2.2 Manual Partition Setup (Alternative)

**Check Available Disks:**

```bash
lsblk
```

**What this does:** Lists all block devices and their partitions  
**Why this matters:** Helps identify the target disk for installation

```bash
ip addr
```

**What this does:** Displays network interface configuration  
**Why this matters:** Confirms network connectivity for package downloads

**Partition the Disk:**

```bash
cfdisk /dev/sda
```

**What this does:** Opens a text-based partition editor  
**Why this matters:** Allows manual control over partition layout  
**Gotcha:** Replace `/dev/sda` with your actual target device

**Format Partitions:**

```bash
mkfs.fat -F32 /dev/sda1
```

**What this does:** Creates a FAT32 filesystem on the EFI partition  
**Why this matters:** UEFI systems require FAT32 for the boot partition

```bash
mkfs.ext4 /dev/sda2
```

**What this does:** Creates an ext4 filesystem on the root partition  
**Why this matters:** ext4 is the standard Linux filesystem with good performance

### 2.3 Mount and Install Base System

```bash
mount /dev/sda2 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
```

**What this does:** Mounts the root and EFI partitions  
**Why this matters:** Prepares the target filesystem for installation

```bash
reflector --country India --latest 5 --sort rate --save /etc/pacman.d/mirrorlist
```

**What this does:** Generates an optimized mirror list for faster downloads  
**Why this matters:** Improves package installation speed  
**Gotcha:** Replace "India" with your country for better speeds

```bash
pacstrap -K /mnt base linux linux-firmware
```

**What this does:** Installs the base Arch Linux system  
**Why this matters:** Provides the minimal system needed to boot

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

**What this does:** Generates filesystem table with UUIDs  
**Why this matters:** Ensures proper mounting after boot

### 2.4 System Configuration

```bash
arch-chroot /mnt
```

**What this does:** Changes root into the newly installed system  
**Why this matters:** Allows configuration of the installed system

```bash
ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
hwclock --systohc
```

**What this does:** Sets the system timezone and syncs hardware clock  
**Why this matters:** Ensures correct time configuration

```bash
pacman -S nano vim
```

**What this does:** Installs text editors  
**Why this matters:** Needed for editing configuration files

**Locale Configuration:**

```bash
nano /etc/locale.gen
```

Uncomment: `en_US.UTF-8 UTF-8`

```bash
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

**What this does:** Configures system language settings  
**Why this matters:** Sets the default system language

**Network Configuration:**

```bash
echo "your-hostname" > /etc/hostname
```

**What this does:** Sets the system hostname  
**Why this matters:** Identifies the system on the network

Edit `/etc/hosts`:

```bash
nano /etc/hosts
```

Add:

```
127.0.0.1   localhost
::1         localhost
127.0.1.1   your-hostname.localdomain your-hostname
```

### 2.5 User and Security Setup

```bash
passwd
```

**What this does:** Sets the root password  
**Why this matters:** Secures the system administrator account

```bash
pacman -S openssh networkmanager sudo grub efibootmgr base-devel git curl qemu-guest-agent
```

**What this does:** Installs essential system packages  
**Why this matters:** Provides networking, SSH, bootloader, and virtualization support

```bash
systemctl enable NetworkManager
systemctl enable sshd
systemctl enable qemu-guest-agent
```

**What this does:** Enables essential services to start at boot  
**Why this matters:** Ensures network and SSH access after reboot

### 2.6 Bootloader Installation

```bash
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

**What this does:** Installs and configures the GRUB bootloader  
**Why this matters:** Makes the system bootable

```bash
exit
umount -R /mnt
reboot
```

## Phase 3: System Enhancements

### 3.1 Swap File Configuration

```bash
sudo fallocate -l 4G /swapfile
```

**What this does:** Creates a 4GB swap file  
**Why this matters:** Provides virtual memory for better system performance

```bash
sudo chmod 600 /swapfile
```

**What this does:** Sets proper permissions on the swap file  
**Why this matters:** Security best practice for swap files

```bash
sudo mkswap /swapfile
sudo swapon /swapfile
```

**What this does:** Initializes and activates the swap file  
**Why this matters:** Makes the swap space immediately available

```bash
echo '/swapfile none swap defaults 0 0' | sudo tee -a /etc/fstab
```

**What this does:** Makes swap permanent across reboots  
**Why this matters:** Ensures swap is available after system restart

**Verification:**

```bash
free -h
swapon --show
```

### 3.2 Development Environment Setup

**Install Neovim with NvChad:**

```bash
sudo pacman -S nvim
```

**What this does:** Installs Neovim text editor  
**Why this matters:** Provides a powerful, modern text editor

```bash
git clone https://github.com/NvChad/starter ~/.config/nvim
```

**What this does:** Installs NvChad configuration for Neovim  
**Why this matters:** Provides a pre-configured IDE-like experience

### 3.3 Shell Environment Enhancement

**Install and Configure Zsh:**

```bash
sudo pacman -S zsh
chsh -s /bin/zsh
```

**What this does:** Installs Zsh and sets it as the default shell  
**Why this matters:** Zsh offers better features than bash

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

**What this does:** Installs Oh My Zsh framework  
**Why this matters:** Provides themes and plugins for enhanced productivity

**Install Useful Plugins:**

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

**What these do:** Add autosuggestions and syntax highlighting to Zsh  
**Why this matters:** Improves command-line productivity and reduces errors

Edit `~/.zshrc` to enable plugins:

```bash
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```

**Install Powerlevel10k Theme:**

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k
```

```bash
sudo pacman -S ttf-hack-nerd
```

**What this does:** Installs a powerline-compatible font  
**Why this matters:** Required for proper theme display

Set theme in `~/.zshrc`:

```bash
ZSH_THEME="powerlevel10k/powerlevel10k"
```

```bash
exec zsh
p10k configure
```

**Additional Tools:**

```bash
sudo pacman -S fzf nnn neovim htop wget tmux fastfetch elinks yt-dlp mpv
```

**What this does:** Installs various productivity and system tools  
**Why this matters:** Provides a complete development environment

## Phase 4: File Sharing Configuration

### 4.1 Install Required Packages (Proxmox Host)

```bash
apt update && apt install -y ntfs-3g samba openssh-server
```

**What this does:** Installs NTFS support, Samba file sharing, and SSH server  
**Why this matters:** Enables file sharing with Windows systems and secure remote access  
**Note:** This runs on the Proxmox host (Debian-based)

### 4.2 Disk Management

**Identify Storage Devices:**

```bash
lsblk -f
```

**What this does:** Lists all block devices with filesystem information  
**Why this matters:** Helps identify NTFS partitions to mount

```bash
fdisk -l
```

**What this does:** Displays detailed partition information  
**Why this matters:** Confirms partition types and sizes

**Fix NTFS Partitions (if needed):**

```bash
ntfsfix /dev/<disk1>
ntfsfix /dev/<disk2>
```

**What this does:** Repairs any NTFS filesystem inconsistencies  
**Why this matters:** Ensures reliable mounting and data integrity  
**Gotcha:** Replace `<disk1>` and `<disk2>` with actual device names

### 4.3 Mount Configuration

**Create Mount Points:**

```bash
mkdir -p /mnt/ntfs1
mkdir -p /mnt/ntfs2
```

**What this does:** Creates directories for mounting NTFS drives  
**Why this matters:** Provides access points for the NTFS filesystems

**Test Manual Mounting:**

```bash
mount -t ntfs-3g /dev/<disk1> /mnt/ntfs1
mount -t ntfs-3g /dev/<disk2> /mnt/ntfs2
```

**What this does:** Mounts NTFS partitions temporarily  
**Why this matters:** Tests mounting before making it permanent  
**Gotcha:** Replace with actual device names from `lsblk` output

**Make Permanent:**

```bash
echo "/dev/<disk1>  /mnt/ntfs1  ntfs-3g  defaults  0  0" >> /etc/fstab
echo "/dev/<disk2>  /mnt/ntfs2  ntfs-3g  defaults  0  0" >> /etc/fstab
```

**What this does:** Adds mount entries to fstab for automatic mounting  
**Why this matters:** Ensures drives are mounted automatically at boot

### 4.4 Samba Configuration

**Backup Original Configuration:**

```bash
cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
```

**What this does:** Creates a backup of the original Samba configuration  
**Why this matters:** Allows rollback if configuration issues occur

**Configure Samba Shares:**

```bash
cat <<EOF >> /etc/samba/smb.conf

[ntfs1]
   path = /mnt/ntfs1
   browseable = yes
   read only = no
   guest ok = no
   valid users = <your-username>

[ntfs2]
   path = /mnt/ntfs2
   browseable = yes
   read only = no
   guest ok = no
   valid users = <your-username>
EOF
```

**What this does:** Adds Samba share definitions for the NTFS drives  
**Why this matters:** Makes the drives accessible over the network  
**Gotcha:** Replace `<your-username>` with your actual username

### 4.5 User Management and Security

**Create System User:**

```bash
adduser <your-username>
```

**What this does:** Creates a new system user account  
**Why this matters:** Provides secure access to file shares

**Set Samba Password:**

```bash
smbpasswd -a <your-username>
```

**What this does:** Adds the user to Samba with a password  
**Why this matters:** Enables authentication for file share access

**Disable Root Samba Access:**

```bash
smbpasswd -x root
```

**What this does:** Removes
