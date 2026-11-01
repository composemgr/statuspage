## 👋 Welcome to statuspage 🚀

Self-hosted status page for monitoring services

## 📋 Description

Cachet is a self-hosted status page for the services you run. You publish
components and incidents, point probes at them, and the page shows current
uptime to anyone who looks at it. This stack runs the `cachethq/docker` image
with a PostgreSQL backend, published on the host nginx proxy.

## 🚀 Services

- **app**: cachethq/docker:latest

### Infrastructure Components

- **statuspage-db**: PostgreSQL database

## 📦 Installation

### Option 1: Quick Install
```bash
curl -q -LSsf "https://raw.githubusercontent.com/composemgr/statuspage/main/docker-compose.yaml" -o compose.yml
```

### Option 2: Git Clone
```bash
git clone "https://github.com/composemgr/statuspage" ~/.local/srv/docker/statuspage
cd ~/.local/srv/docker/statuspage
cp default.env .env
docker compose --env-file .env up -d
```

### Option 3: Using composemgr
```bash
composemgr install statuspage
```

## 🔧 Configuration

### Environment Variables

```shell
TZ=America/New_York
APP_SECRET_KEY=replace-with-a-random-32-byte-hex-string
APP_ENV=production
APP_DEBUG=false
DB_USER_NAME=statuspage
DB_USER_PASS=replace-with-a-random-password
```

`APP_SECRET_KEY` must be exactly 32 characters, Cachet uses it to encrypt
stored settings. `APP_ENV` and `APP_DEBUG` come straight from Laravel, leave
`APP_ENV=production` and `APP_DEBUG=false` outside development.

See `docker-compose.yaml` for complete list of configurable options.

### Environment Files

`default.env.sample` holds the full set of variables and is copied to `.env`
before starting the stack:

```bash
cp default.env .env
docker compose --env-file .env up -d
```

`app.env.sample` holds the app-specific overrides and is copied to `app.env`:

```bash
cp app.env.sample app.env
```

`composemgr up` applies `app.env` and `default.env` automatically. Raw
`docker compose` commands need `--env-file .env` to pick up the same values.

### Reverse Proxy

Three standalone compose files ship in this repo:

| File | Use |
|------|-----|
| `docker-compose.yaml` | Default, published on `172.17.0.1:64355` |
| `docker-compose.traefik.yaml` | Behind an existing external `traefik` network |
| `docker-compose.tunnel.yaml` | Behind an existing external `cloudflare` tunnel network |

```bash
docker compose -f docker-compose.traefik.yaml --env-file .env up -d
```

The proxy variants set `APP_URL` to `https://${BASE_HOST_NAME:-$HOSTNAME}`,
the default file sets it to the published host address. Cachet uses that value
when it builds the links shown on the status page, so change it if you serve
the app from a different address.

## 🌐 Access

- **Web Interface**: http://172.17.0.1:64355

## 📂 Volumes

- `./volumes/data/statuspage` - Uploaded logos, custom CSS and other storage
- `./volumes/db/postgres/statuspage` - Database data

## 🔐 Security

- Set `APP_SECRET_KEY` to a random value of exactly 32 characters, it encrypts
  the application settings
- Change `DB_USER_PASS` before the first start
- Use HTTPS via the reverse proxy in production, a status page is public by
  design and should not leak the admin panel
- The database is reachable only on the internal project network
- Keep `./volumes/db/postgres/statuspage` on a backed-up volume
- Regularly update Docker images for security patches

## 🔍 Logging

```shell
docker compose --env-file .env logs -f app
```

## 🛠️ Management

```bash
# Start services
docker compose --env-file .env up -d

# Stop services
docker compose --env-file .env down

# Update to latest images
docker compose --env-file .env pull && docker compose --env-file .env up -d

# View logs
docker compose --env-file .env logs -f

# Restart services
docker compose --env-file .env restart
```

## 📋 Requirements

- Docker Engine 20.10+
- Docker Compose V2+

## 🤝 Author

🤖 casjay: [Github](https://github.com/casjay) 🤖  
🦄 composemgr: [Github](https://github.com/composemgr) 🦄
