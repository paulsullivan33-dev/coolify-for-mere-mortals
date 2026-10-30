# What Coolify Is

This chapter gives you the idea the whole guide hangs on: what
a **platform as a service** does for you, what Coolify's
version of it looks like, and — stated early, as this series
insists — the responsibility that quietly moves from a
company's staff to you.

## The trick Heroku taught everyone

Deploying a program the traditional way is a pile of separate
jobs. Rent a server. Install the language runtime. Copy the
code across. Work out how to keep it running when it crashes.
Put a web server in front of it. Point a domain at it. Get an
HTTPS certificate, and renew it before it expires. Repeat for
every program, and again for every update.

A platform as a service — **PaaS** — bundles all of that into
one promise: *give us your code, and we will handle the pile.*
Heroku made the promise famous. Vercel and Netlify made it
normal for websites. You push code to a git repository (a
version-controlled folder of your program's source), and the
platform builds it into something runnable, starts it, routes
a domain to it, fetches its certificate, restarts it when it
falls over, and shows you its logs in a dashboard.

## Coolify is that promise, self-hosted

Coolify is free, open-source software (Apache 2.0 licence)
that you install on a Linux server you control — a rented VPS,
a spare machine at home, even a Raspberry Pi. After one
install command (chapter 4 takes it apart), that server has a
web dashboard, reached on port 8000. From the dashboard you:

- connect git repositories from GitHub, GitLab, Gitea,
  Bitbucket, or any public git address, and **deploy** them —
  Coolify builds a Docker image and runs it as a container;
- attach your own **domains**, and Coolify's built-in proxy
  obtains and renews HTTPS certificates automatically;
- add **servers** — other machines Coolify manages over SSH,
  so one dashboard can run things on several computers;
- install **services** — whole ready-made programs such as
  WordPress, Nextcloud or an analytics tool — from a
  catalogue of 353 templates (the count in the project's own
  catalogue file when this guide's lab read it);
- create **databases** — PostgreSQL, MySQL, MariaDB, MongoDB,
  Redis and several more — as managed resources, not as
  weekend projects.

There is no per-application fee and no seat licence: the
software is free, and your costs are the servers themselves.
The project also sells an optional hosted version, Coolify
Cloud, where the company runs the dashboard and it manages
*your* servers; this guide is about the self-hosted original.

## The honest exchange

A hosted platform's price buys more than convenience. It buys
a team that patches the platform at 3 a.m., hardens it against
attack, and answers — contractually — when it breaks. When
you self-host Coolify, every one of those jobs becomes yours:

- **The server is yours to patch**, including Coolify itself
  (it can update itself nightly — chapter 13 covers the choice
  that makes).
- **The security boundary is yours.** The dashboard can run
  commands as root on every server it manages. Whoever reaches
  it as an administrator effectively owns your machines. This
  guide says that plainly wherever it matters, and chapter 13
  collects the whole paragraph in one place.
- **The failures are yours.** If the one server running
  Coolify dies, every application it managed is down with it,
  until you restore it. Chapter 13 is about making that a bad
  afternoon instead of a catastrophe.

None of that makes Coolify a bad idea. For a person's own
projects — the sites, tools, bots and experiments that would
otherwise cost a monthly fee per application on a hosted
platform, or an evening of server chores each — it is a
genuinely good trade. It just is a trade, and it goes better
when you make it knowingly.

## What Coolify is not

- **Not a hosting company.** It provides no servers, no
  bandwidth, no support desk. It is software.
- **Not Kubernetes.** It deliberately stops at "Docker
  containers on servers you can name." If you need a cluster
  scheduler, you need a different tool, and chapter 14 says so
  fairly.
- **Not a backup service.** It can schedule database backups;
  protecting the whole setup is still your design (chapter 13).
- **Not magic about your program.** If your application needs
  a particular runtime version, more memory, or a file it
  cannot find, Coolify will show you the failure in a log —
  it cannot fix the program.
