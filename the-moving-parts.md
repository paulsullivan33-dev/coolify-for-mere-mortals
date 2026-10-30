# The Moving Parts

You will use Coolify more calmly once you can name its pieces.
This chapter inventories them — the dashboard and its own
innards, the machinery it installs on every server it manages,
and the organiser's ladder of teams, projects and environments
that everything you create hangs from. Every item here was
read out of the project's own files by this guide's lab; the
notes are in `coolify-lab/dissection-notes.md`.

## Coolify itself: three containers

On its home server, Coolify runs as a small Docker Compose
stack — defined in the project's `docker-compose.yml` and
`docker-compose.prod.yml`, which the installer downloads:

- **coolify** — the application itself: a Laravel (PHP) web
  app with a Livewire interface. One container carries several
  jobs: the dashboard you browse (published on host port
  **8000**, mapped to 8080 inside the container), a WebSocket
  server (port 6001) that pushes live status into your
  browser, and a terminal server (port 6002) behind the
  dashboard's built-in web terminal. Its health check answers
  at `/api/health`. Its files live under `/data/coolify/` on
  the host — applications, databases, services, backups,
  images, SSH keys — bind-mounted into the container.
- **coolify-db** — a PostgreSQL container holding Coolify's
  own data: your servers, applications, settings, environment
  variables, deployment history. Data in the `coolify-db`
  Docker volume.
- **coolify-redis** — a Redis container used for queues
  (deployments run as background jobs) and caching, protected
  by a generated password.

Older write-ups show a fourth container, `coolify-realtime`.
It is gone: since v4.4 the realtime work runs inside the main
container, and the compose file keeps an empty placeholder so
old overrides do not break upgrades. If a tutorial's diagram
shows four boxes, it is describing that earlier Coolify.

Two more images orbit the stack:

- **coolify-helper** — a utility container the platform runs
  for jobs on the host itself, including the compose commands
  that bring Coolify's own stack up during install and
  upgrade.
- **sentinel** — a small monitoring agent Coolify places on
  managed servers to report their health and usage back to the
  dashboard (the versions file this lab read lists sentinel at
  1.1.3).

## On every managed server: SSH in, Traefik at the door

Coolify does not install an agent you configure by hand. It
manages each server the plain Unix way: it **logs in over
SSH** (with a key it generates and stores), runs Docker
commands, and reads the results. That is why the installer
cares so much about OpenSSH, and why chapter 6 treats the SSH
key as the master key it is.

For web traffic, Coolify starts one more container per
server: **Traefik**, a reverse proxy. Traefik listens on ports
**80 and 443** (plus 443/UDP for HTTP/3) and decides, for each
incoming request, which container should answer. Coolify
writes that decision onto each application container as
Docker labels — `traefik.http.routers…` and friends — so the
proxy configures itself from the containers it can see.
Traefik also fetches and renews the Let's Encrypt HTTPS
certificates. (The series has a whole
[Traefik guide](https://paulsullivan33-dev.github.io/traefik-for-mere-mortals/)
if you want the proxy's own story; here it is a supporting
actor.) The proxy's files live in `/data/coolify/proxy` on
each server.

## The ladder: team, project, environment, resource

Everything you create in the dashboard sits on a fixed ladder,
taken from the project's own domain model:

```
Team → Project → Environment → Resources
```

- A **team** is the top box: it owns servers and members, and
  one person can belong to several teams. Your account starts
  with a personal team.
- A **project** groups the things that belong together — say,
  a website, its API and its database.
- Inside a project, an **environment** is a lane: by default
  one named *production*. You can add more (a *staging* lane,
  for instance), and the same application in two environments
  is two separate deployments that happen to share a recipe.
- A **resource** is anything deployable: an **application**
  (built from git or an image), a **service** (a whole stack
  from the catalogue), or a standalone **database**.

Each resource is placed on a **destination** — in practice, a
Docker network on one of your servers — which is how the same
dashboard deploys one project to the home server and another
to a rented VPS without confusing them.

## Where the truth lives

One consequence of the design matters for later chapters:
Coolify's memory is its PostgreSQL database plus the files in
`/data/coolify/` — the instance `.env` holding its generated
secrets sits in `/data/coolify/source/.env`. Lose the server
without copies of those, and you lose the platform's
knowledge of everything it managed, even if the application
containers' data survives elsewhere. Chapter 13 builds the
backup habit on exactly this fact.
