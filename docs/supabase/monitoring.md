# Monitoring and Troubleshooting

## Monitoring and Logging

### Performance Monitoring

```bash
# Check container resource usage
docker stats
```

**What this does:** Shows real-time container resource usage  
**Why this matters:** Helps monitor performance and resource consumption

**Monitor Service Logs:**

```bash
supabase logs
```

**What this does:** Shows aggregated logs from all Supabase services  
**Why this matters:** Helps troubleshoot issues and monitor health

### Performance Monitoring Commands

```bash
# Monitor resource usage
docker stats --no-stream

# Check disk usage
du -sh ~/projects/supabase-local/

# Monitor database performance
psql postgresql://postgres:postgres@localhost:54322/postgres -c "SELECT * FROM pg_stat_activity;"
```

## Troubleshooting Guide

??? question "Supabase services fail to start"

    **Symptoms:** Error messages during `supabase start`
    **Common Causes:** Port conflicts, insufficient resources, Docker issues

    ```bash
    # Check port availability
    ss -tulpn | grep -E ':54321|:54322|:54323'

    # Free up ports if needed
    sudo fuser -k 54321/tcp 54322/tcp 54323/tcp

    # Restart Docker service
    sudo systemctl restart docker

    # Check Docker daemon logs
    journalctl -u docker.service --since "1 hour ago"
    ```

??? question "Cannot access Supabase dashboard"

    **Symptoms:** Browser cannot reach http://localhost:54323
    **Solution:** Check service status and firewall settings

    ```bash
    # Verify dashboard service is running
    docker ps | grep supabase_studio

    # Check if port is bound correctly
    ss -tulpn | grep :54323

    # Try accessing from different port
    supabase status  # Check actual dashboard URL

    # Restart Supabase services
    supabase stop
    supabase start
    ```

??? question "Database connection refused"

    **Symptoms:** Cannot connect to PostgreSQL database
    **Solution:** Verify database service and credentials

    ```bash
    # Check PostgreSQL container status
    docker ps | grep postgres

    # View PostgreSQL logs
    docker logs supabase_db_postgres

    # Test connection with correct credentials
    supabase status  # Get current DB credentials
    psql "$(supabase status | grep 'DB URL' | cut -d' ' -f3)"
    ```

??? question "Twingate connector not connecting"

    **Symptoms:** Connector shows offline in Twingate admin
    **Solution:** Check tokens and network connectivity

    ```bash
    # Verify connector logs
    docker logs twingate-connector

    # Check network connectivity
    curl -I https://yourdomain.twingate.com

    # Recreate connector with new tokens
    docker-compose down
    # Update tokens in docker-compose.yml
    docker-compose up -d
    ```

??? question "High memory usage"

    **Symptoms:** System running out of memory
    **Solution:** Optimize container resources

    ```bash
    # Check memory usage by container
    docker stats --no-stream

    # Reduce PostgreSQL memory if needed
    # Edit ~/projects/supabase-local/supabase/config.toml
    # Adjust shared_buffers and work_mem settings

    # Restart with new configuration
    supabase stop
    supabase start
    ```

??? question "Slow API response times"

    **Symptoms:** API calls taking too long
    **Solution:** Check database performance and optimize queries

    ```bash
    # Monitor database connections
    psql postgresql://postgres:postgres@localhost:54322/postgres \
      -c "SELECT count(*) FROM pg_stat_activity;"

    # Check for slow queries
    psql postgresql://postgres:postgres@localhost:54322/postgres \
      -c "SELECT query, state, query_start FROM pg_stat_activity WHERE state = 'active';"

    # Optimize database if needed
    psql postgresql://postgres:postgres@localhost:54322/postgres \
      -c "VACUUM ANALYZE;"
    ```
