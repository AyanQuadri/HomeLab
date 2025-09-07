# Docker Setup Guide

## System Preparation

### Docker Installation and Configuration

**Install Docker and Docker Compose:**

```bash
sudo pacman -S docker
```

**What this does:** Installs Docker container runtime on Arch Linux  
**Why this matters:** Docker provides isolated environments for running Supabase services

```bash
sudo pacman -S docker-compose
```

**What this does:** Installs Docker Compose for multi-container orchestration  
**Why this matters:** Supabase requires multiple interconnected services (PostgreSQL, Auth, API, etc.)

**Enable Docker Service:**

```bash
sudo systemctl enable docker
```

**What this does:** Enables Docker to start automatically at boot  
**Why this matters:** Ensures containers restart after system reboots

```bash
sudo systemctl start docker
```

**What this does:** Starts Docker service immediately  
**Why this matters:** Makes Docker available for immediate use

**Add User to Docker Group (Optional):**

```bash
sudo usermod -aG docker $USER
```

**What this does:** Allows running Docker commands without sudo  
**Why this matters:** Improves workflow convenience  
**Gotcha:** Requires logout/login to take effect

### System Resource Configuration

**Check Available Resources:**

```bash
free -h
```

**What this does:** Shows available memory  
**Why this matters:** Supabase requires at least 2GB RAM for optimal performance

```bash
df -h
```

**What this does:** Shows available disk space  
**Why this matters:** Docker images and PostgreSQL data require significant storage

**Verify Docker Installation:**

```bash
docker --version
docker-compose --version
```

**What this does:** Confirms Docker installation  
**Why this matters:** Ensures we have compatible versions

```bash
sudo docker run hello-world
```

**What this does:** Tests Docker functionality with a simple container  
**Why this matters:** Validates Docker is working correctly

## Service Management

### Create Systemd Service for Supabase

**Create Systemd Service for Supabase:**

```bash
sudo tee /etc/systemd/system/supabase.service << EOF
[Unit]
Description=Supabase Local Development
Requires=docker.service
After=docker.service

[Service]
Type=forking
User=<your-username>
WorkingDirectory=<path-to-supabase-project>
ExecStart=/usr/local/bin/supabase start
ExecStop=/usr/local/bin/supabase stop
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF
```

**What this does:** Creates a systemd service for automatic Supabase startup  
**Why this matters:** Ensures Supabase starts automatically after system boot  
**Gotcha:** Replace placeholders with actual username and path

**Enable Supabase Service:**

```bash
sudo systemctl enable supabase.service
sudo systemctl start supabase.service
```

**What this does:** Enables and starts the Supabase service  
**Why this matters:** Makes Supabase management part of system service management
