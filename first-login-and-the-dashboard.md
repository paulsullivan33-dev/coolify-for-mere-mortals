# First Login and the Dashboard

Outcome: you finish this chapter as the registered owner of
your instance, and you can read the dashboard's organisation
at a glance — where servers live, where your work lives, and
where the settings that can hurt you are kept.

## The registration race, run properly

Open the address the installer printed —
`http://<your-server-ip>:8000`. On a fresh install you get a
registration screen, and the first account created becomes
the **root user** of the instance: the administrator of
administrators, owner of the default team, able to see and
change everything. Chapter 4 explained why the window is
short (ten minutes, unless you pre-created the account with
the `ROOT_USER_*` variables, in which case you simply log in
instead). Register with an email address you control and a
password you will not reuse anywhere else — this account is,
in effect, a root login to every server Coolify will manage,
wearing a web form.

Two follow-ups belong to the same sitting:

- **Give the instance its own address.** In the instance
  settings you can set Coolify's own domain (something like
  `coolify.example.com`, with a DNS record pointing at the
  server). The proxy from chapter 2 then serves the dashboard
  over HTTPS like any other application, instead of plain
  HTTP on port 8000. Chapter 13 returns to why that matters.
- **Look at the registration setting.** Once your account
  exists, public registration should be off for everyone else.
  Verify it rather than assuming it.

## Teams: whose stuff is this?

Your account starts inside a **team** — the top of the ladder
from chapter 2. Teams own servers, projects and members;
roles (owner, admin, member) decide who can deploy, who can
change settings, and who can merely watch. A single person
running their own projects can live in the default personal
team forever. The moment you want a second human involved, or
a clean wall between, say, your own projects and a club's,
make a second team rather than sharing your login: a shared
root account is the one convenience this platform cannot make
safe.

## Reading the dashboard

The interface is a live web application (chapter 2's
WebSocket keeps its status lights current), and its geography
is the ladder, flattened into a sidebar:

- **Projects** — your work. Each project opens into its
  environments (production by default), and each environment
  lists its resources: applications, services, databases.
  The **+ New** button inside an environment is where every
  deployment in chapters 8 and 10 begins.
- **Servers** — the machines Coolify manages, starting with
  its own host. Chapter 6 lives here.
- **Sources** — your connections to git providers (a GitHub
  App, a GitLab token, a deploy key), which let Coolify see
  private repositories and redeploy automatically on push.
- **Keys & Tokens** (under settings) — SSH keys Coolify uses,
  and API tokens. The REST API (`/api/v1/`) can do most of
  what the dashboard does; tokens carry abilities (read,
  write, deploy), so a token is a password with narrower
  pockets — issue narrow ones.
- **Settings** — instance-wide choices: the dashboard's own
  domain, registration, update behaviour, and the instance
  `.env`-level values the documentation calls *instance
  environment variables* (for example Coolify's own PHP memory
  limit). Keep the distinction the docs insist on: those tune
  Coolify itself, never your applications.

A habit worth building now: the dashboard shows *state*, and
state can lag or lie after a failure. Every resource page also
has logs, and the deployment log is the ground truth for
chapter 8's work. When the two disagree, believe the log.
