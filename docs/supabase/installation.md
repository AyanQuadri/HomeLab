# Supabase Installation Guide

## Supabase CLI Installation

### Download and Install Supabase CLI

**Download Supabase CLI:**

```bash
curl -sL https://github.com/supabase/cli/releases/latest/download/supabase_linux_amd64.tar.gz -o supabase.tar.gz
```

**What this does:** Downloads the latest Supabase CLI for Linux  
**Why this matters:** The CLI manages Supabase projects and local development environments  
**Gotcha:** Ensure you have internet connectivity for the download

**Extract Archive:**

```bash
tar -xzf supabase.tar.gz
```

**What this does:** Extracts the Supabase binary from the compressed archive  
**Why this matters:** Makes the executable available for installation

**Install to System Path:**

```bash
sudo mv supabase /usr/local/bin/
```

**What this does:** Moves the binary to a system PATH location  
**Why this matters:** Makes the `supabase` command available from anywhere

**Verify Installation:**

```bash
supabase --version
```

**What this does:** Confirms Supabase CLI is installed and shows version  
**Why this matters:** Ensures successful installation

**Clean Up:**

```bash
rm -f supabase.tar.gz
```

**What this does:** Removes the downloaded archive file  
**Why this matters:** Frees up disk space

## Supabase Project Setup

### Initialize Supabase Project

**Create Project Directory:**

```bash
mkdir -p ~/projects/supabase-local
cd ~/projects/supabase-local
```

**What this does:** Creates a dedicated directory for the Supabase project  
**Why this matters:** Keeps project files organized and isolated

**Initialize Supabase:**

```bash
supabase init
```

**What this does:** Creates a new Supabase project with default configuration  
**Why this matters:** Sets up the project structure and configuration files  
**Output:** Creates `supabase/` directory with config and migrations

### Start Supabase Services

**Start All Services:**

```bash
supabase start
```

**What this does:** Downloads and starts all Supabase Docker containers  
**Why this matters:** Launches PostgreSQL, Auth, API, Dashboard, and other services  
**Time:** First run takes 5-10 minutes to download images  
**Gotcha:** Requires significant bandwidth for initial setup

**Monitor Container Status:**

```bash
docker ps
```

**What this does:** Shows running Docker containers  
**Why this matters:** Verifies all Supabase services are running properly

**Check Service Status:**

```bash
supabase status
```

**What this does:** Displays Supabase service URLs and configuration  
**Why this matters:** Provides access information for the Supabase dashboard and APIs

### Configuration Verification

**Access Supabase Dashboard:**

- Local URL: `http://localhost:54323` (default)
- Default credentials are displayed in `supabase status` output

**Test API Connectivity:**

```bash
curl -X GET 'http://localhost:54321/rest/v1/' \
  -H 'apikey: <your-anon-key>'
```

**What this does:** Tests the REST API endpoint  
**Why this matters:** Confirms the API gateway is functioning  
**Gotcha:** Replace `<your-anon-key>` with actual key from `supabase status`

**Check Database Connection:**

```bash
psql postgresql://postgres:postgres@localhost:54322/postgres
```

**What this does:** Connects directly to PostgreSQL database  
**Why this matters:** Verifies database accessibility  
**Gotcha:** Default password is 'postgres' unless configured otherwise
