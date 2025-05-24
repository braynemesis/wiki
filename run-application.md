# Evanthys Application Setup Guide

## Prerequisites

- Docker and Docker Compose installed
- Go 1.24.3 or later
- AWS credentials configured (for SQS queues)
- NextGen API credentials (as specified in config.yaml)

## Project Structure

```
evanthys/
├── cmd/                    # Application entry points
├── config/                 # Configuration files
├── internal/              # Internal application code
├── migrations/            # Database migrations
├── pkg/                   # Public packages
├── config.yaml           # Main configuration file
├── Dockerfile            # Main application Dockerfile
├── Dockerfile.migrate    # Migration Dockerfile
├── docker-compose.yml    # Docker Compose configuration
└── Makefile             # Build and run commands
```

## Configuration

### 1. Database Configuration
The application uses PostgreSQL. Update `config.yaml` with your database credentials:
```yaml
database_url: postgres://evanthys:evanthys@localhost:5432/evanthysdb?sslmode=disable
```

### 2. AWS Configuration
The application uses AWS SQS queues. Make sure your AWS credentials are configured:
```bash
export AWS_PROFILE=evanthys
```

### 3. NextGen Integration
The application integrates with NextGen. Update the credentials in `config.yaml`:
```yaml
integrations:
  nextgen:
    token_url: https://nativeapi.nextgen.com/nge/prod/nge-oauth/token
    base_url: https://nativeapi.nextgen.com/nge/prod/nge-api/api
    client_id: your_client_id
    client_secret: your_client_secret
    site_id: your_site_id
    session_id: your_session_id
    host: nativeapi.nextgen.com
    mode: dev
```

## Building and Running

### 1. Using Docker Compose

#### Start the Database
```bash
# Start PostgreSQL
docker-compose up -d postgres

# Run migrations
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" up
```

#### Build and Run the Application
```bash
# Build the application
docker-compose build

# Run the application
docker-compose up
```

### 2. Using Make Commands

The project includes several Make commands for development:

```bash
# Run the web API
make run-web

# Run the process encounters worker
make run-process-encounters

# Run linter
make lint

# Fix linting issues
make lint-fix
```

### 3. Running Individual Components

The application has multiple components that can be run separately:

#### API Service
```bash
docker-compose run --rm api
```

#### Worker Service
```bash
docker-compose run --rm worker
```

#### Process Encounters Service
```bash
docker-compose run --rm process_encounters
```

## Development Workflow

### 1. Local Development
```bash
# Start the database
docker-compose up -d postgres

# Run migrations
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" up

# Run the application locally
make run-web
```

### 2. Testing Changes
```bash
# Run linter
make lint

# Fix linting issues
make lint-fix

# Run tests (if available)
go test ./...
```

### 3. Database Management

#### Access PostgreSQL
```bash
# Connect to PostgreSQL
docker-compose exec postgres psql -U evanthys -d evanthysdb
```

#### Run Specific Migration
```bash
# Run a specific migration
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" goto VERSION
```

## Production Deployment

### 1. Build Production Image
```bash
# Build the production image
docker build -t evanthys:production .
```

### 2. Run in Production
```bash
# Run with production configuration
docker run -d \
  --name evanthys \
  -p 8080:8080 \
  --env-file .env.production \
  evanthys:production
```

## Monitoring and Maintenance

### 1. View Logs
```bash
# View application logs
docker-compose logs -f

# View specific service logs
docker-compose logs -f api
```

### 2. Database Backup
```bash
# Backup the database
docker-compose exec postgres pg_dump -U evanthys evanthysdb > backup.sql
```

### 3. Database Restore
```bash
# Restore from backup
docker-compose exec -T postgres psql -U evanthys -d evanthysdb < backup.sql
```

## Troubleshooting

### Common Issues

1. **Database Connection Issues**
   - Check if PostgreSQL is running: `docker-compose ps`
   - Verify database credentials in `config.yaml`
   - Check database logs: `docker-compose logs postgres`

2. **Migration Issues**
   - Check migration status: `docker-compose run --rm migrate version`
   - Force to a known good state: `docker-compose run --rm migrate force VERSION`

3. **AWS Connection Issues**
   - Verify AWS credentials: `aws sts get-caller-identity`
   - Check AWS profile: `echo $AWS_PROFILE`

4. **NextGen Integration Issues**
   - Verify NextGen credentials in `config.yaml`
   - Check NextGen API status

## Security Considerations

1. **Environment Variables**
   - Never commit sensitive data to version control
   - Use environment variables for secrets
   - Use `.env` files for local development

2. **Database Security**
   - Use strong passwords
   - Limit database access
   - Regular security updates

3. **API Security**
   - Use HTTPS
   - Implement proper authentication
   - Regular security audits

Would you like me to expand on any of these sections or add additional information?
