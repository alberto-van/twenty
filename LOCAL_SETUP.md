# My Twenty setup

This repository is my local Twenty workspace. It keeps the upstream source available for exploration and customization while running a persistent self-hosted instance with Docker Compose.

## Open Twenty

Twenty is available at [http://localhost:3000](http://localhost:3000).

Docker Desktop must be running before starting the stack.

## Daily commands

Run these commands from `packages/twenty-docker`:

```bash
docker compose up -d
docker compose ps
docker compose logs -f server worker
docker compose down
```

`docker compose down` stops the application without deleting its data. Do not add `-v` unless the database and local file storage should be permanently erased.

## Local data and secrets

- PostgreSQL data lives in the `twenty_db-data` Docker volume.
- Uploaded and locally stored files live in the `twenty_server-local-data` volume.
- `packages/twenty-docker/.env` contains the database password and encryption key. It is ignored by Git and must not be shared or regenerated casually.
- Losing `ENCRYPTION_KEY` can make encrypted application data unreadable. Back up the `.env` file securely before relying on this instance for important data.

## Docker versus source development

The current stack runs the published `twentycrm/twenty` image. Changes made to files in this checkout do not automatically appear in the running application.

For source development and hot reload, use Twenty's contributor workflow instead: run PostgreSQL and Redis from `packages/twenty-docker/docker-compose.dev.yml`, install the Yarn dependencies, initialize the development database, and start the frontend, server, and worker from the checkout.

## Keeping my work separate from upstream

The remotes are intentionally split:

- `origin` is my fork: `git@github.com:alberto-van/twenty.git`
- `upstream` is the original project: `https://github.com/twentyhq/twenty.git`

Fetch upstream changes without pushing personal work there:

```bash
git fetch upstream
git switch main
git rebase upstream/main
git push origin main
```

## Updating the containerized instance

Review upstream release notes before updating, then run:

```bash
docker compose pull
docker compose up -d
docker compose ps
```

The database and file volumes persist across image updates. Keep a backup of the database and `.env` before any important upgrade.
