# Performance Metrics

## System Performance Overview

| Component          | Configuration             | Performance                |
| ------------------ | ------------------------- | -------------------------- |
| **Proxmox Host**   | 8GB RAM, 4 CPU cores      | ~20% average load          |
| **Arch Linux VMs** | 2GB RAM, 2 CPU cores each | ~5% idle CPU usage         |
| **Storage**        | LVM on SSD                | ~150MB/s read/write        |
| **Network**        | Gigabit Ethernet          | Full bandwidth utilization |
| **Samba Shares**   | NTFS-3G mounted           | ~100MB/s file transfers    |

## Resource Monitoring Commands

### System Resource Usage

```bash
# Check memory usage
free -h

# Check disk usage
df -h

# Check CPU usage
htop
```

### Storage Performance

```bash
# Test disk read speed
sudo hdparm -t /dev/sda

# Test disk write speed
dd if=/dev/zero of=/tmp/test bs=1M count=1024 conv=fdatasync

# Check I/O statistics
iostat -x 1
```

### Network Performance

```bash
# Check network interface statistics
cat /proc/net/dev

# Test network bandwidth (using iperf3)
iperf3 -s  # On server
iperf3 -c <server-ip>  # On client

# Monitor network connections
ss -tuln
```

### Virtualization Overhead

```bash
# Check VM resource allocation
qm list

# Monitor VM performance
qm monitor <vmid>

# Check host virtualization load
cat /proc/loadavg
```

## Optimization Tips

### Storage Optimization

- Use SSD storage for VM disks
- Enable discard/trim support
- Configure proper I/O scheduler
- Use thin provisioning for disk space efficiency

### Memory Optimization

- Configure appropriate swap sizes
- Enable memory ballooning for VMs
- Monitor memory usage patterns
- Use hugepages for better performance

### Network Optimization

- Use virtio network drivers
- Configure proper MTU sizes
- Enable network offloading features
- Monitor bandwidth utilization
