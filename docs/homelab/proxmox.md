# Proxmox Host Configuration

## Repository Management

### Disable Enterprise Repository

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

## System Updates

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

## Storage Optimization

```bash
lvremove pve/data
```

**What this does:** Removes the default 'data' logical volume  
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

## SSH Security Hardening

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
