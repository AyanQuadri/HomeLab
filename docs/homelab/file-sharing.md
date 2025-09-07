# File Sharing Configuration

## Install Required Packages (Proxmox Host)

```bash
apt update && apt install -y ntfs-3g samba openssh-server
```

**What this does:** Installs NTFS support, Samba file sharing, and SSH server  
**Why this matters:** Enables file sharing with Windows systems and secure remote access  
**Note:** This runs on the Proxmox host (Debian-based)

## Disk Management

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

## Mount Configuration

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

## Samba Configuration

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

## User Management and Security

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

**What this does:** Removes root user from Samba access  
**Why this matters:** Improves security by preventing root-level file share access

**Restart Samba Services:**

```bash
systemctl restart smbd nmbd
```

**What this does:** Restarts Samba file sharing services  
**Why this matters:** Applies configuration changes and enables shares
