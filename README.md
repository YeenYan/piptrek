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

## Fixing Laravel 500 Server Error (Missing `.env`)

If you get a **500 Server Error** after starting the containers, it's likely because the `.env` file is missing inside the backend container.

**1. Enter the backend container:**

```bash
docker compose exec backend sh
```

**2. Create the `.env` file:**

```bash
cat > .env <<EOL
APP_NAME=PipTrek
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost:8000
LOG_CHANNEL=stderr

DB_CONNECTION=pgsql
DB_HOST=db
DB_PORT=5432
DB_DATABASE=piptrek
DB_USERNAME=piptrek
DB_PASSWORD=piptrek_secret

CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_CONNECTION=sync

JWT_SECRET=
EOL
```

**3. Generate the app key and JWT secret:**

```bash
php artisan key:generate
php artisan jwt:secret
```

**4. Clear Laravel caches:**

```bash
php artisan config:clear
php artisan cache:clear
```

> **Note:** The `.env` inside the container is lost on rebuild. For permanent setup, create `be-piptrek/.env` in the project directory so Docker always loads it automatically.

---

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
