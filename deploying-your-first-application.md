# Deploying Your First Application

Outcome: a program that lives in a public git repository is
running as a container on your server, started from the
dashboard, with a deployment log you can read. This is the
platform's central workflow, described from the official
documentation (the lab could not click through it — see
`criteria.md`), and it is the workflow chapters 9 to 12 then
refine.

## The shape of every deployment

Strip any Coolify deployment to its bones and five pieces
remain, in this order:

1. a **destination** — the server (and its Docker) the thing
   will run on;
2. a **source** — where the code comes from;
3. a **build strategy** — how the code becomes an image
   (chapter 9);
4. **runtime settings** — the port the program listens on,
   its environment variables (chapter 12), its domain
   (chapter 11);
5. the **deploy** itself — a queued job that builds, starts,
   health-checks, and swaps the new container in.

## The worked first run: a public repository

Start inside a project, in the **production** environment,
and choose **+ New → Application**. For a first deployment,
pick the simplest source Coolify offers: a **public git
repository** — you paste a URL, no account connection needed.

1. **Paste the repository URL** and the branch (usually
   `main`). Coolify fetches the repository and looks at what
   it contains.
2. **Choose the build pack.** Coolify proposes one from what
   it detected — a framework it recognises, a `Dockerfile` if
   one is present, or finished files for a static site. For
   the first run, accept the proposal; chapter 9 explains the
   menu you are choosing from (Nixpacks, Railpack, Static,
   Dockerfile, Docker Compose, or a prebuilt Docker image,
   which skips building entirely).
3. **Set the port.** The application must tell Coolify which
   port its program listens on inside the container — the
   single most common first-deploy mistake is a wrong port
   here, because everything else can be perfect and the
   result is still a dead page.
4. **Save, then Deploy.** Deployment runs as a background job
   (on the Redis queue from chapter 2). Its log streams into
   the dashboard: clone, build steps, image creation,
   container start. Read your first one end to end — it is
   the platform explaining itself, and chapter 16's
   troubleshooting starts from these lines.
5. **Open it.** With no domain yet, the application is
   reachable on the server through its generated address;
   chapter 11 gives it a proper name and HTTPS.

## The second deployment is the point

A platform earns its keep on redeploys, not on the premiere.
Two ways to trigger them:

- **By hand:** the Deploy button again (with a *force deploy*
  variant that builds without the layer cache — the right
  choice when a build looks stale rather than broken).
- **Automatically:** connect the repository properly — via a
  **GitHub App** or a webhook — and every push to the watched
  branch deploys itself. Private repositories need this
  connection in any case: the GitHub App (or a deploy key for
  other providers) is also what lets Coolify *see* the code,
  and what enables the pull-request previews of chapter 7.

Set the automatic path up once the manual one works. An
auto-deploy you do not yet understand is just a surprise that
schedules itself.
