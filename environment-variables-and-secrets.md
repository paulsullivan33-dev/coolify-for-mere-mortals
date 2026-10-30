# Environment Variables and Secrets

Outcome: you know where an application's configuration
lives, how values differ between build time and run time and
between environments, and which file on the server you must
never let escape.

Programs take their settings from **environment variables** —
named values (`DATABASE_URL`, `API_KEY`, `PORT`) handed to
the process when it starts. Twelve-factor orthodoxy aside,
the practical point is simple: the same image runs anywhere
because the *values* change, not the code. Coolify is where
you keep the values.

## Where variables live

- **Per application.** Each application resource has an
  environment-variables editor in the dashboard. Values set
  here are injected into that application's containers at
  deploy time — which means **changing a variable means
  redeploying**, not nudging a running container. The new
  value takes effect when the new container starts.
- **Build-time versus runtime.** Builds need some values too
  (a public API address baked into a frontend bundle is the
  classic). Coolify distinguishes variables available during
  the build from those available only at runtime; a value
  that seems ignored is often simply in the wrong of the two
  boxes.
- **Shared variables.** Repeating the same value across five
  applications is how values drift apart. Coolify offers
  **shared variables** at team, project and environment
  level, which resources can reference instead of copying —
  one place to change, everywhere updated at next deploy.
  Use them for the values that truly are common (a mail
  server's address); keep a genuinely per-application secret
  on the application.
- **Per environment.** Values are scoped by the ladder of
  chapter 7: production's database password and staging's
  are different variables that share a name. That separation
  is a safety feature — protect it by never "just copying
  prod's values across to test something quickly" as a habit.

## The secrets, honestly

A variable marked as a secret is still a value in a database:
Coolify's own PostgreSQL holds your applications' settings,
protected by the instance's credentials and by who can log
into the dashboard. That is a normal, reasonable design —
and it has two consequences worth stating as rules.

1. **Dashboard access is secret access.** Anyone who can
   administer a team can read its applications' variables.
   Team membership (chapter 5) is therefore a secrets
   decision, not a social one.
2. **The host is the vault.** Root on the server reads
   `/data/coolify/source/.env` (Coolify's *own* secrets —
   its database and Redis passwords, its app key) and can
   inspect any container's environment. The installer's
   parting warning, from chapter 4, bears repeating verbatim
   in spirit: **back that `.env` up somewhere safe outside
   the server** — you need it to rebuild the instance, and
   nobody else should ever find it lying around.

Two working habits finish the chapter:

- **Never commit real secrets to the git repository.** The
  repository is the one artefact that travels — clones,
  forks, provider backups. Repositories carry variable
  *names* and, at most, example values; real values live in
  Coolify.
- **Rotate like you mean it.** When a secret leaks (a log, a
  screenshot, a departed collaborator), change it at the
  source, update it in Coolify, and redeploy. Editing the
  copy in the dashboard alone changes nothing the outside
  world knows.
