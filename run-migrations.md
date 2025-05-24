I'll document all the important migration commands and their purposes:

### Basic Migration Commands

1. **Run all pending migrations**
```bash
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" up
```
- This runs all migrations that haven't been applied yet
- Migrations are run in order based on their version numbers
- If any migration fails, the process stops

2. **Rollback the last migration**
```bash
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" down 1
```
- The `1` at the end means rollback one migration
- You can change the number to rollback multiple migrations

3. **Check current migration version**
```bash
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" version
```
- Shows the current version of the database
- Useful to see which migrations have been applied

### Advanced Migration Commands

4. **Force a specific version**
```bash
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" force VERSION
```
- Forces the database to a specific migration version
- Useful when you need to skip a problematic migration
- Example: `force 20250425131828`

5. **Run specific number of migrations**
```bash
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" up 1
```
- The `1` at the end means run one migration
- You can change the number to run multiple migrations

6. **Go to a specific version**
```bash
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" goto VERSION
```
- Migrates the database to a specific version
- Will run up or down migrations as needed to reach the target version

### Troubleshooting Commands

7. **Reset database and run migrations**
```bash
# Stop containers
docker-compose down

# Remove the volume to delete the database
docker volume rm evanthys_postgres_data

# Start fresh database
docker-compose up -d postgres

# Run migrations
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" up
```
- Complete reset of the database
- Useful when you want to start fresh

8. **Fix dirty database state**
```bash
# Force to last successful migration
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" force LAST_SUCCESSFUL_VERSION

# Then run remaining migrations
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" up
```
- Used when a migration fails and leaves the database in a "dirty" state
- First force to a known good state, then continue with remaining migrations

### Common Patterns

1. **Skip a problematic migration**
```bash
# Force to the version before the problematic migration
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" force VERSION_BEFORE_PROBLEM

# Force to the version after the problematic migration
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" force VERSION_AFTER_PROBLEM

# Run remaining migrations
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" up
```

2. **Check migration status and fix issues**
```bash
# Check current version
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" version

# If there are issues, force to a known good state
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" force KNOWN_GOOD_VERSION

# Continue with migrations
docker-compose run --rm migrate -path /migrations -database "postgres://evanthys:evanthys@postgres:5432/evanthysdb?sslmode=disable" up
```

Remember:
- Always backup your database before running migrations in production
- Test migrations in a development environment first
- Keep track of which migrations have been applied
- Use version control for your migration files
- Document any manual steps needed for migrations

Would you like me to explain any of these commands in more detail?
