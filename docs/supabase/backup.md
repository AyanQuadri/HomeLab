# Backup Configuration

## Backup Configuration

### Create Backup Script

**Create Backup Script:**

```bash
cat > ~/scripts/backup-supabase.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/home/$(whoami)/backups/supabase"
DATE=$(date +%Y%m%d_%H%M%S)

# Create backup directory
mkdir -p "$BACKUP_DIR"

# Backup PostgreSQL database
docker exec supabase_db_postgres pg_dump -U postgres postgres > "$BACKUP_DIR/postgres_$DATE.sql"

# Backup Supabase configuration
cp -r ~/projects/supabase-local/supabase "$BACKUP_DIR/config_$DATE"

# Compress backups older than 1 day
find "$BACKUP_DIR" -name "*.sql" -mtime +1 -exec gzip {} \;

# Remove backups older than 30 days
find "$BACKUP_DIR" -name "*.gz" -mtime +30 -delete

echo "Backup completed: $DATE"
EOF
```

**Make Script Executable:**

```bash
chmod +x ~/scripts/backup-supabase.sh
```

**What this does:** Creates an automated backup solution  
**Why this matters:** Protects against data loss and configuration issues

**Schedule Regular Backups:**

```bash
crontab -e
```

Add:

```bash
0 2 * * * /home/<username>/scripts/backup-supabase.sh >> /var/log/supabase-backup.log 2>&1
```

**What this does:** Schedules daily backups at 2 AM  
**Why this matters:** Ensures regular, automated data protection

### Log Rotation Configuration

**Set Up Log Rotation:**

```bash
sudo tee /etc/logrotate.d/docker << EOF
/var/lib/docker/containers/*/*.log {
    rotate 7
    daily
    compress
    size=1M
    missingok
    delaycompress
    copytruncate
}
EOF
```

**What this does:** Configures automatic log rotation for Docker containers  
**Why this matters:** Prevents log files from consuming excessive disk space