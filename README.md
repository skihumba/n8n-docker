# N8N Docker Deployment

This repository contains a Docker Compose configuration for running [n8n](https://n8n.io/), an extendable workflow automation tool, alongside PostgreSQL for data storage and ngrok for secure tunneling.

## Overview

This setup includes:

1. **n8n** - The workflow automation platform
2. **PostgreSQL** - Database for storing n8n workflows and data
3. **ngrok** - Secure tunneling service to expose n8n to the internet

## Prerequisites

- Docker and Docker Compose installed on your system
- Basic understanding of Docker and containerization
- An ngrok account and authtoken (for the tunneling feature)
- Environment variables configured (see Configuration section)

## Quick Start

1. Clone this repository:
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. Create a `.env` file in the project root (see Environment Variables section)

3. Start the containers:
   ```bash
   docker-compose up -d
   ```

4. Access n8n at:
   - Local: `http://localhost:5678` (default port)
   - Via ngrok: The URL provided by your ngrok domain configuration

## Environment Variables

Create a `.env` file in the project root with the following variables:

```
# PostgreSQL Configuration
POSTGRES_USER=n8n_user
POSTGRES_PASSWORD=your_secure_password
POSTGRES_DB=n8n
DB_TYPE=postgresdb
DB_PORT=5432

# n8n Configuration
N8N_PROTOCOL=http
N8N_HOST=localhost
N8N_PORT=5678
WEBHOOK_URL=https://your-ngrok-domain.ngrok.io
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=your_secure_password
N8N_RUNNERS_ENABLED=true
N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=false
N8N_DEFAULT_BINARY_DATA_MODE=filesystem
TZ=UTC

# ngrok Configuration
NGROK_DOMAIN=your-ngrok-domain
NGROK_AUTHTOKEN=your_ngrok_authtoken
```

Replace placeholder values with your actual configuration details.

## Service Details

### PostgreSQL
- **Image**: postgres:15
- **Container Name**: postgres-n8n
- **Purpose**: Primary database for n8n
- **Data Persistence**: Uses a Docker volume named `postgres_data`

### n8n
- **Image**: n8nio/n8n
- **Container Name**: n8n
- **Purpose**: Workflow automation platform
- **Data Persistence**: Uses a Docker volume named `n8n_data`
- **Dependencies**: Requires PostgreSQL to be running
- **Port**: Defaults to 5678 (configurable via `.env`)

### ngrok
- **Image**: ngrok/ngrok:latest
- **Container Name**: ngrok
- **Purpose**: Provides secure tunneling to expose n8n to the internet
- **Dependencies**: Requires n8n to be running
- **Configuration**: Requires a valid ngrok authtoken and domain

## Data Persistence

This setup uses Docker volumes for data persistence:

- `postgres_data`: Stores PostgreSQL database files
- `n8n_data`: Stores n8n workflows and data

## Networking

All services are connected through a Docker bridge network named `n8n`.

## Security Considerations

- Change all default passwords in the `.env` file
- Consider additional security measures if deploying in a production environment
- Basic authentication for n8n is enabled by default (configurable via `.env`)

## Troubleshooting

### Common Issues

1. **Database connection errors**:
   - Ensure PostgreSQL container is running: `docker ps`
   - Check database credentials in the `.env` file
   - Verify n8n can reach PostgreSQL container via the network

2. **ngrok connection issues**:
   - Verify your ngrok authtoken is valid
   - Ensure your ngrok domain is correctly configured
   - Check ngrok container logs: `docker logs ngrok`

3. **n8n not starting**:
   - Check container logs: `docker logs n8n`
   - Verify environment variables are correctly set

## Maintenance

### Updating

To update to the latest versions:

```bash
docker-compose pull
docker-compose down
docker-compose up -d
```

### Backup

To backup your data:

1. Export n8n workflows from the UI
2. Backup PostgreSQL data:
   ```bash
   docker exec postgres-n8n pg_dump -U n8n_user -d n8n > n8n_backup.sql
   ```

## Additional Resources

- [n8n Documentation](https://docs.n8n.io/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [ngrok Documentation](https://ngrok.com/docs)
