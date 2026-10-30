# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   Example domains are `example.com` and its subdomains.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 15 names what the tool
   cannot do: the single point of failure, builds that run on
   your own hardware, the deliberate stop at Docker, a
   community catalogue that is not a contract, and the platform
   team of one that the reader becomes.
8. **The ethics rule is in the text, early.** For this guide
   the rule is the operator's one, and chapter 1 states it
   before any procedure depends on it: a Coolify dashboard is
   root access to every server it manages, so winning the
   first-registration race, closing public registration,
   guarding the instance `.env`, and treating team membership
   as a secrets decision are duties, not tips. Chapter 13
   collects the whole paragraph in one place.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a 2-CPU, 7.7 GB Ubuntu 24.04 sandbox,
shared with other work while this guide was written. It has
**no Docker daemon and no way to start containers**, and
Coolify's installer — the program this guide teaches —
reconfigures its entire host (root shell, Docker install and
daemon settings, an added root SSH key, ports claimed). Running
it on a shared machine was therefore impossible twice over,
and it was not attempted: no installer run, no Docker command,
no container, no dashboard session. The core of this guide is
**documented-only**, and the README, chapter 4 and chapter 15
all say so in the text.

In place of running it, the lab dissected the project's own
artefacts — the full notes, with versions and URLs, are in
`coolify-lab/dissection-notes.md`:

- The repository `github.com/coollabsio/coolify`, shallow
  clone at commit `751ecb91c59fdb35fe076e5bba7076651ea2652c`
  (2026-10-30); current stable **v4.5.4** per its
  `versions.json`. Clone deleted after dissection.
- `scripts/install.sh` read in full (1,179 lines): the
  supported-OS list and refusals, the disk check that warns
  (30 GB total / 20 GB free) rather than refusing, the nine
  named steps, Docker's ≥ 24 hard gate, the daemon.json
  changes and their backup, the generated `.env` secrets, the
  ed25519 key appended to root's `authorized_keys`, the
  10-minute root-account warning and the `.env` backup
  warning, both printed by the script itself.
- `docker-compose.yml` and `docker-compose.prod.yml` read in
  full: the three running services (coolify on host port
  8000→8080, ports 6001/6002; `coolify-db` PostgreSQL;
  `coolify-redis`), the `/data/coolify/` bind mounts, the
  health check, and the zero-replica `soketi` placeholder
  whose comment dates the realtime-in-one-container change to
  v4.4.
- The proxy generation code (`bootstrap/helpers/proxy.php`):
  Traefik image `traefik:v3.7`, published ports 80, 443,
  443/UDP and 8080, routing by generated Docker labels.
- The domain model from the repo's own `AGENTS.md` and
  `app/Models/`: Team → Project → Environment → Resources;
  the standalone database list; sentinel and helper versions.
- `app/Enums/BuildPackTypes.php`, verbatim: `nixpacks`,
  `static`, `dockerfile`, `dockercompose`, `railpack` — and
  the repo's own written guidance that Nixpacks is deprecated
  in favour of the Dockerfile build pack or Railpack.
- `templates/service-templates-latest.json` counted directly:
  **353 service templates**, sampled from the actual list.
- Official documentation read on 2026-10-30: the install and
  requirements page (`coolify.io/docs/start-with-self-hosted`)
  for the 2-core / 2 GB / 10 GB minimums, supported OS table,
  fresh-server advice and the `ROOT_USER_*` pre-creation
  option; the builds overview
  (`coolify.io/docs/applications/builds/overview`) for the
  build-strategy map and build-server behaviour; the static
  build-pack page for the Nginx packaging and domain fields.
  URLs are recorded in the dissection notes.

Documented only — from the sources above, not run, and the
text does not claim otherwise:

- Every dashboard workflow: registration and first login,
  adding and validating servers, creating projects and
  environments, the first deployment and its log, choosing
  build packs, installing services and databases, entering
  domains, editing variables, scheduling backups.
- Behaviours stated by the documentation but not visible in
  the files read: preview deployments' exact screens,
  Let's Encrypt issuance timing, update cadence details
  beyond the `AUTOUPDATE` setting the script exposes.
- The neighbours of chapter 14 (Heroku, Vercel, Netlify,
  Railway, Dokploy, CapRover, Kubernetes) — characterised
  fairly from general knowledge, not benchmarked.

Fixtures in this guide are deliberately generic: a bookshop
site and a club booking system stand in as example projects,
and all domains are `example.com` and its subdomains. No real
server, organisation or person is described.
