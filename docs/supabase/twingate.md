# Twingate Integration for Secure Access

## Twingate Integration for Secure Access

### Twingate Account Setup

**Prerequisites:**

- Create a Twingate account at [twingate.com](https://www.twingate.com)
- Set up your network in the Twingate Admin Console
- Generate a connector token for your deployment

### Twingate Connector Deployment

**Create Connector Directory:**

```bash
mkdir -p ~/twingate
cd ~/twingate
```

**What this does:** Creates a dedicated directory for Twingate configuration  
**Why this matters:** Keeps Twingate files separate from other services

**Create Docker Compose for Twingate:**

```bash
cat > docker-compose.yml << EOF
version: '3.7'
services:
  twingate-connector:
    image: twingate/connector:1
    container_name: twingate-connector
    environment:
      - TWINGATE_ACCESS_TOKEN=<your-access-token>
      - TWINGATE_REFRESH_TOKEN=<your-refresh-token>
      - TWINGATE_URL=<your-network>.twingate.com
    restart: unless-stopped
    sysctls:
      - net.ipv4.ping_group_range=0 2147483647
    networks:
      - twingate
networks:
  twingate:
    driver: bridge
EOF
```

**What this does:** Creates a Docker Compose configuration for Twingate connector  
**Why this matters:** Provides persistent and manageable Twingate connectivity  
**Gotcha:** Replace placeholder values with actual tokens from Twingate admin console

**Deploy Twingate Connector:**

```bash
docker-compose up -d
```

**What this does:** Starts the Twingate connector in the background  
**Why this matters:** Establishes secure tunnel to Twingate network

**Verify Connector Status:**

```bash
docker-compose logs -f twingate-connector
```

**What this does:** Shows connector logs to verify connection  
**Why this matters:** Confirms successful registration with Twingate network

!!! note "**Easy Way:** You can just copy paste the docker command with the binaries!"

### Network Access Configuration

**Configure Resource Access in Twingate Admin:**

1. **Add Resources:**

      - Supabase Dashboard: `http://localhost:54323`
      - Supabase API: `http://localhost:54321`
      - PostgreSQL: `localhost:54322`

2. **Create Access Policies:**

      - Define user groups with appropriate permissions
      - Set up conditional access based on device/location
      - Configure session timeouts and security policies

3. **Deploy Client Applications:**

      - Install Twingate client on devices requiring access
      - Authenticate users and verify connectivity