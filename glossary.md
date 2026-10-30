# Glossary

The terms this guide uses, collected. Plain definitions,
in the order you are most likely to go looking for them.

**Platform as a service (PaaS)** — a system that takes your
code and handles the pile underneath it: building, running,
routing, certificates, restarts. Heroku is the famous one;
Coolify is the self-hosted one this guide teaches.

**Deploy / deployment** — one act of turning a version of
your code into running containers, and the record of it.
In Coolify a deployment is a background job with a log.

**Build pack / build strategy** — the recipe that turns a
repository into a Docker image: Railpack, Nixpacks, Static,
Dockerfile or Docker Compose in Coolify's menu.

**Docker image** — the packaged, read-only form of a
program: its files, runtime and start command, frozen.
**Container** — a running instance of an image.

**Docker Compose** — Docker's way of defining several
containers in one YAML file and running them as a unit.
Coolify's own stack, its services, and one of its build
packs all speak Compose.

**Destination** — where a resource runs: in practice, a
Docker network on one managed server.

**Server (managed server)** — a machine Coolify drives over
SSH. Its own host is the first one, called the localhost
server.

**Build server** — a managed server that only builds images,
sparing the servers that run applications.

**Team / Project / Environment / Resource** — Coolify's
organising ladder: who owns things, what the thing is, which
lane it lives in, and the deployable thing itself.

**Preview deployment** — a temporary copy of an application,
created automatically for a pull request and removed when
the request closes.

**Service** — a complete program installed from Coolify's
template catalogue (353 templates when this guide was
written), as opposed to an application you build from your
own repository.

**Standalone database** — a database server (PostgreSQL,
MySQL, MariaDB, MongoDB, Redis, ClickHouse, KeyDB,
Dragonfly) run by Coolify as a resource in its own right.

**Reverse proxy** — the bouncer at a server's web door:
accepts requests on ports 80/443 and hands each to the right
container. Coolify uses **Traefik**, configured per server
through Docker labels.

**Let's Encrypt** — the free certificate authority whose
certificates Traefik requests and renews automatically, so
HTTPS needs no manual certificate chores.

**Environment variable** — a named value handed to a program
as it starts (`DATABASE_URL`, `API_KEY`). Coolify stores
them per application, with shared variables at team, project
and environment level.

**`.env` file** — a text file of such variables. Coolify's
own instance `.env`, at `/data/coolify/source/.env`, holds
the platform's generated secrets and is the single most
important file to back up off the server.

**Sentinel** — the small monitoring agent Coolify places on
managed servers to report health and usage.

**Helper** — `coolify-helper`, a utility container Coolify
runs on its host for install, upgrade and housekeeping jobs.
