# Coolify for Mere Mortals

Heroku taught a generation of programmers a lovely trick: push
your code to a git repository, and a platform builds it, runs
it, gives it a web address, renews its HTTPS certificate, and
keeps it alive — while you think about your program instead of
your servers. The trick came with a bill, and with rules you
did not write. **Coolify** is the same trick as free software
you install on a server of your own. You give it a machine; it
gives you back a web dashboard where you connect git
repositories, press Deploy, attach your own domains, and add
ready-made services — a database here, a whole WordPress there
— from a catalogue of hundreds. The server stays yours. So
does the job of looking after it, and this guide does not hide
that part: the last chapters are about backups, updates, and
exactly what you have taken on.

This guide explains what a platform like this actually does,
takes the install script apart line by line so you know what it
will do to your machine before you run it, and then walks the
whole working day of the thing: adding servers, organising
projects, deploying an application from a public git
repository, choosing how it gets built, standing up services
and databases in a few clicks, and giving everything a proper
domain with HTTPS that renews itself.

The lab behind this guide could not run Coolify, and the guide
says so wherever it matters. Coolify is installed by a script
that takes over a machine — it installs Docker, rewrites
Docker's settings, and adds its own SSH key to the root
account — and the lab machine is a shared sandbox with no
Docker in it at all. Running the installer there was never an
option. Instead, the lab read the project's own files: the
real install script (all 1,179 lines), the Docker Compose
files that define Coolify's own stack, the proxy configuration
the program generates, the list of build packs and the
353-entry service catalogue, and the official documentation
for every workflow. That turns out to be a good way to write
this particular guide, because the install script *is* the
install — reading it closely is the skill chapter 4 teaches.
What the files could not settle, the text does not invent:
screen layouts are described only as far as the documentation
describes them. `criteria.md` keeps the full ledger of what
was read and what could not be run.

## What this guide covers

- [**What Coolify Is**](what-coolify-is.md) —
  the platform-as-a-service idea, what you hand over and what
  you keep, and the responsibility that moves to you
- [**The Moving Parts**](the-moving-parts.md) —
  the dashboard container and its database, the helper and the
  sentinel, the Traefik proxy, and servers managed over SSH
- [**Before You Install**](before-you-install.md) —
  the server, the sizes the documentation asks for versus the
  checks the script itself makes, a domain, and the ports
- [**Installing Coolify**](installing-coolify.md) —
  the one command, taken apart into its nine steps: Docker,
  daemon settings, generated secrets, and its own SSH key
- [**First Login and the Dashboard**](first-login-and-the-dashboard.md) —
  the ten-minute race for the root account, teams, and how the
  dashboard organises everything you will make
- [**Servers — This Machine and Others**](servers.md) —
  the host Coolify manages first (its own), and adding further
  machines over SSH, including a separate build server
- [**Projects and Environments**](projects-and-environments.md) —
  the Team → Project → Environment → Resource ladder, and
  what an environment really is
- [**Deploying Your First Application**](deploying-your-first-application.md) —
  a public git repository to a running container, step by
  step, and how later pushes redeploy
- [**Build Packs**](build-packs.md) —
  Railpack, the Dockerfile, static sites, Docker Compose, and
  the ageing Nixpacks — how to choose, honestly
- [**One-Click Services and Databases**](one-click-services-and-databases.md) —
  the 353-template catalogue sampled fairly, and databases as
  things Coolify runs for you
- [**Domains and Automatic HTTPS**](domains-and-automatic-https.md) —
  the Traefik proxy, the domain field that makes a route, and
  Let's Encrypt certificates that renew themselves
- [**Environment Variables and Secrets**](environment-variables-and-secrets.md) —
  where an application's settings live, shared variables, and
  why Coolify's own `.env` file is a crown jewel
- [**Backups, Updates and Staying Safe**](backups-updates-and-staying-safe.md) —
  what to copy off the machine, the nightly auto-update, and
  the security paragraph this guide owes you, plainly
- [**The Neighbours**](the-neighbours.md) —
  Heroku, Vercel and Netlify, Dokploy, and plain Docker
  Compose by hand: what each does that Coolify doesn't
- [**Limits**](limits.md) — one operator's platform: the
  single point of failure, build storms, and the work no
  dashboard removes
- [**Troubleshooting**](troubleshooting.md) —
  the locked-out dashboard, the deploy that never starts, the
  certificate that never arrives, and where the logs live
- [**Glossary**](glossary.md) — the terms, collected in one
  place

## What it doesn't cover

Coolify Cloud, the project's hosted dashboard (it manages
your servers but is not self-hosting, so it is a different
product for this guide's purposes). Kubernetes — Coolify is
deliberately not that. Writing the applications themselves:
this guide deploys a program, it does not teach programming.
Running other people's production businesses: the chapters on
backups and security are written for a person running their
own projects, and a team with customers should treat them as
a floor, not a ceiling.

## How to use this guide

Read chapter 1 for the idea and chapter 2 before installing
anything — most confusion later is a moving part you did not
know existed. Chapter 3 is the shopping list; do not skip its
port and disk paragraphs. Chapter 4 rewards slow reading: it
is the chapter that makes the one-line install an informed
decision instead of a leap. Chapters 5 to 8 get you a
dashboard, a server, a project, and a first deployment in one
sitting. The lab's dissection notes live in
`coolify-lab/dissection-notes.md` — every version number,
port, and file path in this guide traces back to them.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Docker for Mere Mortals](https://paulsullivan33-dev.github.io/docker-for-mere-mortals/)
- [Docker Compose for Mere Mortals](https://paulsullivan33-dev.github.io/docker-compose-for-mere-mortals/)
- [Traefik for Mere Mortals](https://paulsullivan33-dev.github.io/traefik-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/coolify-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/coolify-for-mere-mortals/blob/main/LICENSE-CODE).
