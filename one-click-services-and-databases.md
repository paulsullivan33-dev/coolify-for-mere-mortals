# One-Click Services and Databases

Outcome: you can judge the catalogue honestly — what "one
click" really buys, what it quietly skips — and you know the
difference between a *service* and a *database* in Coolify's
vocabulary, because they are maintained differently.

## Services: whole programs from a catalogue

A **service** is a complete, pre-configured application
stack that Coolify deploys from a template: the program, its
companion database if it needs one, its volumes, its default
settings — written down once, as a Docker Compose definition,
by the project and its community. In the dashboard it is one
resource under **+ New → Service**; behind the scenes,
Coolify parses the template, generates the secrets it calls
for, and runs it like the compose stacks of chapter 9.

The catalogue is large. The template file in the project's
repository held **353 services** when this guide's lab read
it, and an honest sample — not the highlights reel, just the
spread — includes WordPress, Ghost and other publishing
tools; Nextcloud, Seafile and file tools; GitLab, Gitea and
Forgejo for code; Jellyfin, Plex, Emby and Navidrome for
media; Immich for photos; Paperless for documents; Home
Assistant and Pi-hole for the house; n8n, Flowise and
Activepieces for automation; Uptime Kuma, Beszel and Grafana
for watching things; MinIO, Supabase and Appwrite for
building on; Vaultwarden for passwords; SearXNG for search;
a Minecraft server, a Palworld server, and several hundred
more.

What one click genuinely buys: a *known-good composition*.
Someone has already worked out which image tags pair, which
environment variables the program demands, which port it
listens on, and what its health check looks like. For a
self-hoster, that is most of the work of adopting a new tool,
and it is why the catalogue is Coolify's most loved feature.

What one click does not buy, stated as plainly:

- **Backups are not included.** The template stands the
  program up; where its data lives and how it gets copied
  off the machine is still your arrangement (chapter 13).
- **Defaults are a starting point.** Generated admin
  passwords and secrets are shown in the service's settings
  in the dashboard — read them, record the ones you need in
  your own password manager, and change what the program
  itself asks you to change at first login.
- **The catalogue is a commons.** Templates track upstream
  projects of varying health. A famous name is not a promise
  that its template is freshly maintained; check when the
  program itself last released before you build a habit on
  it.

## Databases: the same idea, one layer down

A **database** resource is a single database server, run by
Coolify as a first-class thing rather than as a sidecar in
somebody's compose file. The standalone list, from the
project's own models: **PostgreSQL, MySQL, MariaDB, MongoDB,
Redis, ClickHouse, KeyDB and Dragonfly**. You create one
inside an environment, set (or let Coolify generate) its
credentials, and applications — on the same server — connect
to it over the Docker network. Whether it is also reachable
from outside the server is a setting, and the right default
answer for a database is *no*: a database exposed to the
internet is an incident with a fuse already lit.

Why make a database a resource of its own instead of burying
it in each application's compose file? Because databases are
the part of a system with a memory. As their own resources
they get their own lifecycle in the dashboard — their own
backups (chapter 13), their own logs, their own upgrades you
perform deliberately rather than as a side effect of
redeploying a website. The trade is a little more structure
to learn, and it is the trade this guide recommends for
anything whose data you would miss.

One sharp edge, named here so it cannot surprise you later:
Coolify's *own* database — the `coolify-db` PostgreSQL of
chapter 2 — is not one of these resources and not yours to
experiment with. It is the platform's memory. The databases
in this chapter are for *your* applications; keep the two
ideas in separate drawers.
