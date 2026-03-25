# PipTrek

Full-stack monorepo — Vue 3 frontend, Laravel 12 backend (GraphQL), PostgreSQL database.

## Project Structure

```
infra-piptrek/
├── fe-piptrek/            # Frontend (Vue 3 + Vite → Nginx)
├── be-piptrek/            # Backend  (Laravel 12 + PHP-FPM + Nginx)
├── docker-compose.yml
└── README.md
```

## Getting Started

Clone the repository:

```bash
git clone <repository-url>
cd infra-piptrek
```

If the project uses submodules:

```bash
git clone --recurse-submodules <repository-url>
```

Or if already cloned without submodules:

```bash
git submodule update --init --recursive
```

Build and start the containers:

```bash
docker compose up -d --build
```

Run database migrations (first time only):

```bash
docker exec -it piptrek-backend php artisan migrate --force
```

## Accessing the Services

| Service    | URL                                                                         |
| ---------- | --------------------------------------------------------------------------- |
| Frontend   | http://localhost:3000                                                       |
| Backend    | http://localhost:8000                                                       |
| GraphQL    | http://localhost:8000/graphql                                               |
| PostgreSQL | localhost:5432 (user: `piptrek`, password: `piptrek_secret`, db: `piptrek`) |

## Stop the Containers

```bash
docker compose down
```

To also remove volumes (wipes DB data):

```bash
docker compose down -v
```

## Useful Commands

```bash
# View logs
docker compose logs -f

# View logs for a specific service
docker compose logs -f backend

# Enter backend container
docker exec -it piptrek-backend sh

# Run artisan commands
docker exec -it piptrek-backend php artisan <command>
```
