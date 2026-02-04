# mattermost-postgres-bigm

PostgreSQL Docker image with [pg_bigm](https://github.com/pgbigm/pg_bigm) extension pre-installed for Mattermost full-text search support.

pg_bigm provides 2-gram (bigram) full-text search capability, which is particularly useful for non-alphabetic languages (e.g., Japanese, Chinese, Korean) and short keyword searches.

## Usage

### Build the image

```bash
docker build -t mattermost-postgres:14 .
```

### Run the container

```bash
docker run -d --name postgres \
  -e POSTGRES_USER=mmuser \
  -e POSTGRES_PASSWORD=mostest \
  -p 5432:5432 \
  mattermost-postgres:14
```

This starts a PostgreSQL 14 container with pg_bigm installed, accessible on port **5432**

### Test pg_bigm installation

Create a test database and enable the extension:

```bash
# Create a database
PGPASSWORD=mostest psql -h localhost -p 5432 -U mmuser -d postgres -c "CREATE DATABASE mattermost_test;"

# Enable pg_bigm extension and verify installation
PGPASSWORD=mostest psql -h localhost -p 5432 -U mmuser -d mattermost_test -c "CREATE EXTENSION IF NOT EXISTS pg_bigm; SELECT extname, extversion FROM pg_extension WHERE extname = 'pg_bigm';"
```

### Stop and remove the container

```bash
docker stop postgres && docker rm postgres
```

## Maintenance

### Updating pg_bigm version

1. Check the latest release at [pg_bigm releases](https://github.com/pgbigm/pg_bigm/releases)
2. Update the download URL in `Dockerfile` with the new version
3. Rebuild the image: `docker build --no-cache -t mattermost-postgres:14 .`

### Updating PostgreSQL version

1. Update the `FROM postgres:XX` line in `Dockerfile` (e.g., `postgres:15`)
2. Update the `postgresql-server-dev-XX` package version to match
3. Rebuild the image with the appropriate tag: `docker build --no-cache -t mattermost-postgres-bigm:15 .`

**Note:** Check pg_bigm compatibility with your PostgreSQL version.

## Resources

- [pg_bigm Releases](https://github.com/pgbigm/pg_bigm/releases)
- [pg_bigm Documentation](https://github.com/pgbigm/pg_bigm/blob/REL1_2_STABLE/docs/pg_bigm_en.md)
- [pg_bigm GitHub Repository](https://github.com/pgbigm/pg_bigm)
