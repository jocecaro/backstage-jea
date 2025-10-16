# Docker Deployment Guide

This guide covers building and running Backstage in Docker containers.

## Quick Start with Docker Compose

The easiest way to run Backstage locally with Docker is using Docker Compose:

```bash
# 1. Install dependencies and build
yarn install
yarn tsc
yarn build:backend

# 2. Start services (PostgreSQL + Backstage)
docker-compose up -d

# 3. View logs
docker-compose logs -f backstage

# 4. Access the application
# Open http://localhost:7007 in your browser

# 5. Stop services
docker-compose down
```

## Building the Docker Image

### Prerequisites

Before building, you must:

1. Install dependencies: `yarn install`
2. Build TypeScript: `yarn tsc`
3. Build the backend: `yarn build:backend`

### Build Command

```bash
# Build using the workspace script
yarn build-image

# Or build directly with Docker
docker build . -f packages/backend/Dockerfile -t backstage:latest
```

### What Gets Built

The Dockerfile creates a production-ready image that:

- Uses Node.js 22 (slim Debian base)
- Installs native dependencies (Python, C++ compiler, SQLite)
- Copies only production dependencies
- Includes the built backend bundle
- Runs as non-root `node` user

## Running with Docker Compose

The `docker-compose.yml` includes:

### Services

1. **PostgreSQL** - Production database

   - Image: `postgres:15-alpine`
   - Port: 5432
   - Persistent volume for data

2. **Backstage** - Main application
   - Built from local Dockerfile
   - Port: 7007
   - Depends on PostgreSQL

### Environment Variables

Configure via `.env` file (copy from `.env.example`):

```bash
cp .env.example .env
# Edit .env and add your values
```

Key variables:

- `GITHUB_TOKEN` - GitHub personal access token (optional but recommended)
- `POSTGRES_*` - Database credentials
- `AWS_*` - AWS credentials/configuration (for AWS plugins)

## Manual Docker Run

If you prefer not to use Docker Compose:

```bash
# 1. Start PostgreSQL
docker run -d \
  --name backstage-postgres \
  -e POSTGRES_USER=backstage \
  -e POSTGRES_PASSWORD=backstage \
  -e POSTGRES_DB=backstage \
  -p 5432:5432 \
  postgres:15-alpine

# 2. Start Backstage
docker run -d \
  --name backstage-app \
  -p 7007:7007 \
  -e POSTGRES_HOST=backstage-postgres \
  -e POSTGRES_PORT=5432 \
  -e POSTGRES_USER=backstage \
  -e POSTGRES_PASSWORD=backstage \
  --link backstage-postgres \
  backstage:latest
```

## Troubleshooting

### Build Failures

**Issue**: "skeleton.tar.gz not found"

**Solution**: Run `yarn build:backend` before building the Docker image.

**Issue**: "Permission denied" errors during build

**Solution**: Ensure Docker BuildKit is enabled:

```bash
export DOCKER_BUILDKIT=1
docker build ...
```

### Runtime Issues

**Issue**: Database connection errors

**Solution**:

- Check PostgreSQL is running: `docker-compose ps`
- Verify connection settings in environment variables
- Wait for PostgreSQL to initialize (check with `docker-compose logs postgres`)

**Issue**: "Port already in use"

**Solution**:

- Check if another service is using port 7007: `lsof -i :7007`
- Stop the conflicting service or change the port in `docker-compose.yml`

### Performance Issues

**Issue**: Slow startup or high memory usage

**Solution**:

- Increase Docker resource limits (Docker Desktop settings)
- Use production database (PostgreSQL) instead of SQLite
- Ensure adequate disk space for volumes

## Production Considerations

### Database

For production:

- Use Amazon RDS for PostgreSQL
- Configure connection pooling
- Set up automated backups
- Use SSL/TLS connections

Example production database config:

```yaml
backend:
  database:
    client: pg
    connection:
      host: ${POSTGRES_HOST}
      port: ${POSTGRES_PORT}
      user: ${POSTGRES_USER}
      password: ${POSTGRES_PASSWORD}
      ssl:
        ca: ${POSTGRES_CA_CERT}
        rejectUnauthorized: true
```

### Security

- Use secrets management (AWS Secrets Manager, HashiCorp Vault)
- Don't commit credentials to Git
- Use read-only file systems where possible
- Enable HTTPS/TLS
- Configure Content Security Policy (CSP)

### Monitoring

Add health checks to `docker-compose.yml`:

```yaml
services:
  backstage:
    healthcheck:
      test:
        [
          'CMD',
          'wget',
          '--no-verbose',
          '--tries=1',
          '--spider',
          'http://localhost:7007/healthcheck',
        ]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

### Logging

Configure structured logging:

```yaml
backend:
  log:
    format: json
    level: info
```

View logs:

```bash
docker-compose logs -f backstage
docker logs backstage-app
```

## Multi-Stage Build (Alternative)

For smaller images, you can use a multi-stage build. See the [Backstage documentation](https://backstage.io/docs/deployment/docker#multi-stage-build) for details.

## Next Steps

- [Deploy to AWS ECS](../k8s/README.md)
- [Deploy to AWS EKS](../k8s/README.md)
- [Configure AWS Plugins](./AWS_PLUGINS.md)
- [Set up CI/CD pipeline](https://backstage.io/docs/deployment/cicd)
