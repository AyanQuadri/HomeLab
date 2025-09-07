# Access Control and Security

## Verification Commands

Use these commands to verify your Supabase deployment is working correctly:

### Service Health Check

```bash
# Check all Supabase services
supabase status

# Verify Docker containers
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

### API Connectivity Tests

```bash
# Test REST API
curl -X GET 'http://localhost:54321/rest/v1/' \
  -H 'apikey: <your-anon-key>' \
  -H 'Content-Type: application/json'

# Test Auth API
curl -X GET 'http://localhost:54321/auth/v1/settings' \
  -H 'apikey: <your-anon-key>'
```

### Database Connectivity

```bash
# Connect to PostgreSQL
psql postgresql://postgres:postgres@localhost:54322/postgres -c '\dt'

# Check database size
psql postgresql://postgres:postgres@localhost:54322/postgres -c "SELECT pg_size_pretty(pg_database_size('postgres'));"
```

### Network and Security Verification

```bash
# Check listening ports
ss -tulpn | grep -E ':54321|:54322|:54323'

# Verify Twingate connector status
docker logs twingate-connector --tail 20

# Test internal connectivity
nmap -p 54321-54323 localhost
```