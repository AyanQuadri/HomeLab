# Networking Configuration

## System Health Check

```bash
# Check system resources
free -h
df -h
lsblk
```

## Network Services Status

```bash
# Check service status
systemctl status smbd
systemctl status nmbd
systemctl status ssh
```

## File Share Testing

```bash
# Test Samba shares
smbclient -L localhost -U <your-username>
```

## Network Connectivity

```bash
# Check listening ports
ss -tulpn | grep -E ':22|:139|:445'

# Test SSH access
ssh <your-username>@<server-ip>
```

## Storage Verification

```bash
# Check mounted filesystems
mount | grep ntfs
cat /etc/fstab | grep ntfs
```
