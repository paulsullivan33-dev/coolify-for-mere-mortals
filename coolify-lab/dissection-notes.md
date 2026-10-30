# Coolify lab — dissection notes

This guide's lab could not run Coolify. The build machine is a
shared sandbox with no Docker daemon and no way to start
containers, and Coolify's installer reconfigures the whole host
(root shell, Docker installation, daemon settings, SSH keys), so
running it on a machine shared with other work would have been
wrong even if Docker had existed. Instead, the lab read the
project's own artefacts — the same files a reader's server would
download — and the official documentation. Everything below was
read on 2026-10-30.

## The source tree

- Repository: https://github.com/coollabsio/coolify
- Clone: shallow (`git clone --depth 1`), deleted after dissection.
- Commit: `751ecb91c59fdb35fe076e5bba7076651ea2652c`
  (2026-10-30 23:19 +0100, the v4.x development line).
- Current stable version at dissection time, from the repo's own
  `versions.json`: **Coolify v4 4.5.4**; nightly 4.6-rc.1;
  helper image 1.0.19; sentinel 1.1.3; Traefik pins listed up to
  v3.7 (3.7.16).
- The project's own `TECH_STACK.md` / `AGENTS.md` describe the
  application itself: Laravel 12 on PHP 8.5, Livewire 3 UI,
  PostgreSQL for its own data, Redis for queues, a Laravel
  Reverb WebSocket server (port 6001) and a Node terminal
  WebSocket server (port 6002), both inside the main container.

## The installer — `scripts/install.sh` (1,179 lines, read in full)

This is the file served from
`https://cdn.coollabs.io/coolify/install.sh`. Facts the guide
relies on, in the script's own order:

- Refuses to run as non-root: "Please run this script as root or
  with sudo."
- Supported OS list (the `case` that exits otherwise): Arch,
  Ubuntu, Debian, Raspbian, CentOS, Fedora, RHEL, Oracle Linux,
  Rocky, SLES, openSUSE Leap/Tumbleweed, AlmaLinux, Amazon
  Linux, Alpine, postmarketOS, TencentOS; several derivatives
  (Manjaro, Pop!_OS, Mint, Zorin…) are normalised to their base.
  Anything else gets "This script only supports Debian, Redhat,
  Arch Linux, Alpine Linux, or SLES based operating systems for
  now."
- Disk check is a **warning, not a refusal**: total disk under
  30 GB or free disk under 20 GB prints a warning, sleeps 5
  seconds, and continues. (The documentation's minimums —
  2 cores, 2 GB RAM, 10 GB free — are lower; see docs below.)
- The installer works as **9 numbered steps** on screen:
  1. Installing required packages — `curl wget git jq openssl`
     via the distro's package manager.
  2. Checking OpenSSH server configuration — detects sshd; if
     absent, **installs and enables OpenSSH server itself**;
     warns if `PermitRootLogin` is disabled; refuses to proceed
     if Docker was installed via snap.
  3. Installing Docker Engine (or "Checking Docker Engine") —
     via `curl -fsSL https://get.docker.com | sh`, with a manual
     apt-repo fallback on Debian/Ubuntu; **Docker below version
     24 is a hard error** and aborts the install.
  4. Configuring Docker daemon — writes log rotation
     (json-file, 10 MB × 3 files) and a default address pool
     (10.0.0.0/8, size 24) into `/etc/docker/daemon.json`,
     backing the original up as `daemon.json.original-<date>`;
     it will *not* restart a Docker daemon that has running
     containers unless `DOCKER_POOL_FORCE_OVERRIDE=true`.
  5. Downloading required files from CDN — `docker-compose.yml`,
     `docker-compose.prod.yml`, `.env.production`, `upgrade.sh`,
     `upgrade-postgres.sh` into `/data/coolify/source/`.
  6. Setting up environment variables — generates random secrets
     with openssl into `/data/coolify/source/.env`: `APP_ID`,
     `APP_KEY`, `DB_PASSWORD`, `REDIS_PASSWORD`,
     `PUSHER_APP_ID/KEY/SECRET`, `PUSHER_BACKEND_PORT=6001`.
     Predefined root account supported via `ROOT_USERNAME`,
     `ROOT_USER_EMAIL`, `ROOT_USER_PASSWORD` environment
     variables. `AUTOUPDATE=false` disables auto-updates.
  7. Checking SSH key for localhost access — creates
     `/data/coolify/{applications,databases,backups,services,proxy,proxy/dynamic,sentinel,images,ssh}`
     (owner 9999, mode 700), generates an ed25519 key at
     `/data/coolify/ssh/keys/id.<user>@host.docker.internal`,
     and **appends its public half to root's
     `~/.ssh/authorized_keys`** — Coolify manages its own host
     over SSH, like any other server.
  8. Pulling images — hands off to `upgrade.sh`, which runs the
     `coollabsio/coolify-helper` container to bring the stack up.
  9. Starting Coolify — waits for the upgrade status file, then
     for the `coolify` container's health check, and prints the
     dashboard addresses as `http://<ip>:8000`, ending with two
     warnings quoted in chapter 4: create the root account
     within 10 minutes on a fresh install, and back up
     `/data/coolify/source/.env` somewhere off the server.

## The core stack — `docker-compose.yml` + `docker-compose.prod.yml`

- `coolify` — image `coollabsio/coolify`, container name
  `coolify`. Publishes `"${APP_PORT:-8000}:8080"` (dashboard on
  host port 8000), the Reverb port (6001) and 6002. Bind-mounts
  `/data/coolify/{ssh,applications,databases,services,backups,images}`
  and the instance `.env`. Health check curls `/api/health`.
  Depends on postgres and redis being healthy.
- `postgres` — container name `coolify-db`, volume `coolify-db`
  → `/var/lib/postgresql/data`; database name defaults to
  `coolify`, user/password from the instance `.env`.
- `redis` — container name `coolify-redis`, volume
  `coolify-redis`; runs with `--requirepass` from the `.env`.
- Base file pins `postgres:15-alpine` and `redis:7-alpine`.
- A `soketi` service remains in the prod file with **zero
  replicas**: realtime used to be a separate container before
  v4.4 and now runs inside the main `coolify` container (the
  file's own comment says so). Older guides showing a separate
  `coolify-realtime` container describe that earlier layout.

## The proxy — `bootstrap/helpers/proxy.php`, `app/Actions/Proxy/`

- The reverse proxy is **Traefik**, started per managed server
  by Coolify itself (it is not part of the compose stack above).
- The generated proxy compose uses image `traefik:v3.7` (the pin
  in the source at dissection time) and publishes `80:80`,
  `443:443`, `443:443/udp` (HTTP/3) and `8080:8080` (Traefik's
  own dashboard/API, internal).
- Applications are routed by generated `traefik.*` Docker
  labels; the proxy configuration lives under
  `/data/coolify/proxy` on each managed server.

## Source layout used to name the moving parts

- `app/Models/` — `Server`, `Application`, `Service`,
  `Project`, `Environment`, `Team`, and standalone database
  models (`StandalonePostgresql`, `StandaloneMysql`, …).
- The project's own domain summary (repo `AGENTS.md`):
  hierarchy is **Team → Project → Environment → Resources**;
  a *Server* is a managed host connected via SSH; a *Service*
  is a pre-configured stack from the templates file; standalone
  databases offered are PostgreSQL, MySQL, MariaDB, MongoDB,
  Redis, ClickHouse, KeyDB, Dragonfly (and SQLite).
- Build packs, from `app/Enums/BuildPackTypes.php`, verbatim:
  `nixpacks`, `static`, `dockerfile`, `dockercompose`,
  `railpack`.
- One-click catalogue: `templates/service-templates-latest.json`
  contains **353 service templates** at dissection time —
  including WordPress, Ghost, Nextcloud, GitLab, Gitea,
  Jellyfin, Plex, Immich, n8n, Uptime Kuma, MinIO, Vaultwarden,
  Supabase, Appwrite, Paperless, Home Assistant, Pi-hole, and
  hundreds more. `templates/compose/` holds 387 compose files.
- Project guidance in the repo (`AGENTS.md`) states plainly:
  "Nixpacks is deprecated. Never recommend it to users…
  Recommend the Dockerfile build pack, or Railpack where it
  fits." The guide follows that steer and says so.

## Official documentation read (2026-10-30)

- Install / requirements —
  https://coolify.io/docs/start-with-self-hosted
  (minimums: 2 CPU cores, 2 GB RAM, 10 GB free disk, amd64 or
  arm64; fresh server recommended; supported-OS table;
  `ROOT_USER_*` pre-creation so "public registration is never
  exposed").
- Build strategies overview —
  https://coolify.io/docs/applications/builds/overview
  (Nixpacks, Railpack, Static and Dockerfile each build one
  application image; Docker Compose can build or pull per
  service; prebuilt Docker Image applications skip the build
  stage; builds run on the deployment server by default, a
  separate build server is supported).
- Static build pack —
  https://coolify.io/docs/applications/builds/static
  (packages finished files into an Nginx image; Base Directory
  setting; domain added under **Domains** with protocol,
  hostname, and optional port/path).
- The install script itself links the OpenSSH knowledge-base
  page: https://coolify.io/docs/knowledge-base/server/openssh

## What was NOT done

No installer run, no Docker command, no container, no dashboard
session. Every workflow in chapters 5–13 is documented from the
sources above, and criteria.md keeps the ledger.
