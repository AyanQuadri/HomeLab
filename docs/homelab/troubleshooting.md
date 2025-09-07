# Troubleshooting Guide

## Common Issues and Solutions

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

## Network Troubleshooting

### Connectivity Issues

```bash
# Test network connectivity
ping 8.8.8.8

# Check routing table
ip route show

# Verify DNS resolution
nslookup google.com

# Check network interface status
ip addr show
```

### Service Issues

```bash
# Check all listening services
ss -tuln

# Verify service status
systemctl status <service-name>

# Check service logs
journalctl -u <service-name> -f
```

### Firewall Issues

```bash
# Check iptables rules
iptables -L -n

# Check ufw status (if installed)
ufw status

# Test port connectivity
telnet <host> <port>
```

## Performance Troubleshooting

### High CPU Usage

```bash
# Check top processes
top

# Check system load
uptime

# Monitor CPU usage over time
sar -u 1 10
```

### Memory Issues

```bash
# Check memory usage
free -h

# Check swap usage
swapon -s

# Find memory-consuming processes
ps aux --sort=-%mem | head
```

### Storage Issues

```bash
# Check disk usage
df -h

# Find large files
find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null

# Check I/O wait
iostat -x 1
```

## Emergency Recovery

### Boot Issues

```bash
# Boot from rescue media
# Mount root filesystem
mount /dev/sda2 /mnt

# Chroot into system
arch-chroot /mnt

# Fix bootloader
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

### File System Corruption

```bash
# Check filesystem
fsck /dev/sda2

# Force filesystem check
fsck -f /dev/sda2

# Repair filesystem
fsck -y /dev/sda2
```

### Service Recovery

```bash
# Reset failed services
systemctl reset-failed

# Reload systemd configuration
systemctl daemon-reload

# Restart all services
systemctl restart <service-name>
```
