# Installation

ASAP can be deployed in multiple ways depending on your needs. Choose the method that works best for you.

## Prerequisites

- **Docker & Docker Compose** (recommended)
- **Node.js 18+** and **PostgreSQL 14+** (for manual installation)

---

## Method 1: Docker Compose (Recommended)

All deployment files live in the [`deploy/`](https://github.com/asap-open/ASAP/tree/main/deploy) directory. Two compose files are provided — one that spins up a PostgreSQL container for you, and one for connecting to a database you already have running.

::: tip
Generate a secure JWT secret with: `openssl rand -base64 32`
:::

### Step 1: Create a directory on your homelab and enter it

```bash
mkdir asap && cd asap
```

### Step 2: Download the compose file and its matching env file

**Option A — let Docker manage the database:**

```bash
curl -O https://raw.githubusercontent.com/asap-open/ASAP/refs/heads/main/deploy/compose.with-db.yaml
curl -O https://raw.githubusercontent.com/asap-open/ASAP/refs/heads/main/deploy/.env.with-db
```

**Option B — bring your own database:**

```bash
curl -O https://raw.githubusercontent.com/asap-open/ASAP/refs/heads/main/deploy/compose.external-db.yaml
curl -O https://raw.githubusercontent.com/asap-open/ASAP/refs/heads/main/deploy/.env.external-db
```

### Step 3: Copy the env file to `.env` and fill in your values

```bash
# Option A
cp .env.with-db .env

# Option B
cp .env.external-db .env
```

Open `.env` in your editor and update at minimum `JWT_SECRET`, `DOMAIN_NAME`, and (for Option B) `DATABASE_URL`.

### Step 4: Start the stack

```bash
# Option A
docker compose -f compose.with-db.yaml up -d

# Option B
docker compose -f compose.external-db.yaml up -d
```

### Step 5: Access ASAP

Open your browser and navigate to:

- **Frontend**: [http://localhost](http://localhost)
- **Backend API**: [http://localhost:3000](http://localhost:3000)

### Step 6: Create Your Account

1. Click "Sign Up" on the homepage
2. Enter your details (username, email, password, full name)
3. Start tracking your workouts!

---

## Method 2: Docker (Separate Containers)

### Run PostgreSQL

```bash
docker run -d \
  --name asap-db \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=asap \
  -p 5432:5432 \
  postgres:16-alpine
```

### Run Backend

```bash
docker run -d \
  --name asap-server \
  --link asap-db:db \
  -e DATABASE_URL="postgresql://postgres:postgres@db:5432/asap" \
  -e JWT_SECRET="your-jwt-secret" \
  -e PORT=3000 \
  -p 3000:3000 \
  sarthakg0yal/asap-server
```

### Run Frontend

```bash
docker run -d \
  --name asap-client \
  -e BACKEND_SERVER_URL="<BACKEND_URL>"
  -p 80:80 \
  sarthakg0yal/asap-client
```

---

## Method 3: Manual Installation (Development)

For development or if you prefer not to use Docker:

### Step 1: Clone and Install Dependencies

```bash
# Clone repository
git clone https://github.com/yourusername/asap.git
cd asap

# Install server dependencies (also runs prisma generate via postinstall)
cd server
yarn install

# Install client dependencies
cd ../client
yarn install
```

### Step 2: Setup PostgreSQL

Install PostgreSQL and create a database:

```bash
createdb asap
```

### Step 3: Configure Server Environment

Create `server/.env`:

```bash
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/asap"
JWT_SECRET="your-jwt-secret"
TOKEN_EXP="7d"
PORT=3000
NODE_ENV=development
FRONTEND_DOMAIN="http://localhost:5173"
```

| Variable          | Required | Description                                        |
| ----------------- | -------- | -------------------------------------------------- |
| `DATABASE_URL`    | ✅       | PostgreSQL connection string                       |
| `JWT_SECRET`      | ✅       | Secret for signing JWTs — use a long random string |
| `TOKEN_EXP`       | ✅       | Token lifetime (e.g. `7d`, `24h`, `60m`)           |
| `PORT`            | ✅       | Port the API server listens on                     |
| `NODE_ENV`        | ✅       | Set to `development`                               |
| `FRONTEND_DOMAIN` | ✅       | Client origin allowed by CORS (Vite dev = `:5173`) |

::: tip
Generate a secure JWT secret with: `openssl rand -base64 32`
:::

### Step 4: Configure Client Environment

Create `client/.env`:

```bash
BACKEND_SERVER_URL="http://localhost:3000"
DOMAIN_NAME="localhost"
```

| Variable             | Required | Description                                                         |
| -------------------- | -------- | ------------------------------------------------------------------- |
| `BACKEND_SERVER_URL` | ✅       | URL of the API server — used by Vite's dev proxy                    |
| `DOMAIN_NAME`        |          | Public hostname — used for HMR and `allowedHosts`, optional locally |

### Step 5: Run Database Migrations

```bash
cd server
npx prisma migrate dev
```

> **Note:** `prisma generate` runs automatically as part of `yarn install` (via the `postinstall` hook). If you ever update the schema manually, re-run `npx prisma generate` to rebuild the client.

### Step 6: Seed Initial Data (Optional)

```bash
cd server
npx prisma db seed
```

### Step 7: Start Development Servers

In one terminal (backend):

```bash
cd server
yarn run dev
```

In another terminal (frontend):

```bash
cd client
yarn run dev
```

Access the application:

- **Frontend**: [http://localhost:5173](http://localhost:5173)
- **Backend API**: [http://localhost:3000/api](http://localhost:3000/api)

---

## Troubleshooting

### Database Connection Issues

If you see database connection errors:

```bash
# Check if PostgreSQL is running
docker ps | grep postgres

# View database logs
docker logs asap-db
```

### Port Already in Use

If ports 3000 or 5173 are already in use:

```bash
# Change ports in docker-compose.yaml or .env files
# Backend port: modify PORT in .env
# Frontend port: modify docker-compose.yaml ports section
```

### Migration Failures

If database migrations fail:

```bash
# Reset database (warning: deletes all data)
cd server
npx prisma migrate reset

# Or manually run migrations
npx prisma migrate deploy
```

---

## Next Steps

- [Quick Start Guide](/guide/quick-start)
- [API Documentation](/api/authentication)
- [Feature Overview](/guide/features/sessions)

::: tip Need Help?
Open an issue on GitHub or check the troubleshooting guide.
:::
