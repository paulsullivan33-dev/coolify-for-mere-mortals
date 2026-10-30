# Troubleshooting

Failures on a platform like this cluster into a few shapes.
Each entry below starts from the symptom you actually see.
Where a cause lives in a file this guide's lab read, the
entry says which; the dashboard workflows are the
documentation's. The universal first move, before any of
them: **read the deployment log**. It is the platform
explaining itself, and most of these entries end there.

## The dashboard is unreachable

- **The container isn't healthy.** On the host:
  `docker ps` should show `coolify`, `coolify-db` and
  `coolify-redis` up, and `docker logs coolify` shows the
  application booting or failing. The health check answers
  at `http://<host>:8000/api/health` (8000 is the published
  port from the compose file).
- **The port is closed.** Chapter 3's list: 8000 must reach
  you — your provider's firewall as well as the machine's.
  If you closed 8000 after giving the dashboard a domain
  (chapter 11), use the domain; that closure working as
  intended looks exactly like this failure.
- **Locked out entirely.** The installer's escape hatch: if
  the registration window closed before you made the root
  account, `docker restart coolify` reopens it (chapter 4).
  For a forgotten password, the documentation's account
  recovery flows start from the login page and the instance's
  mail settings; the root shell on the host is the final
  backstop, as it is for everything in this guide.

## A deployment never starts, or sits queued

Deployments are background jobs on Coolify's Redis queue. A
deploy that never moves usually means the machinery, not
your code: check the three core containers are healthy, then
the server the resource targets — is it validated and
reachable over SSH (Servers page)? A managed server that
lost its SSH path (key changed, firewall tightened, machine
rebuilt) fails every job aimed at it while looking merely
quiet. The deployment log's first lines tell you which stage
never began.

## The build fails

- **Detection guessed wrong.** The build log shows which
  build pack ran and what it assumed. Overrides first
  (install/build/start commands), a `railpack.json` or
  `nixpacks.toml` second, and the Dockerfile strategy
  (chapter 9) when guessing itself is the problem.
- **The build ran out of machine.** Out-of-memory kills and
  mysteriously truncated builds on a small server are
  chapter 15's limit arriving on schedule: watch the server
  during a build; the durable fixes are a bigger server or a
  build server (chapter 6).
- **Stale-cache weirdness.** A build that succeeds but ships
  yesterday's behaviour is the cache's fault until proven
  otherwise — *force deploy (without cache)* is the one-click
  experiment that settles it.

## The site loads as a proxy error (502/504)

The proxy answered, so DNS and Traefik are alive; the
container behind the name is not answering. In order:
container actually running (`docker ps` on the target
server); the application's **port** setting matches the port
the program really listens on (chapter 8's classic); the
program binds beyond localhost inside its container (a
server listening only on `127.0.0.1` inside the container
cannot be reached by the proxy — it must listen on all its
interfaces). The proxy's own logs, under `/data/coolify/proxy`
on the server, show what Traefik believed.

## The certificate never arrives

Walk chapter 11's chain: the name resolves to this server
(check from outside, not from the server itself); port 80
reaches the server (the usual challenge needs it); the
domain entry in Coolify uses `https://` and was followed by a
redeploy (labels are written at deploy time); and Let's
Encrypt's rate limits have not been spent by a day of
repeated attempts — if they have, the cure is waiting, not
retrying harder.

## A service from the catalogue won't start

Read the service's own logs in the dashboard first — most
template failures are a program complaining about its own
configuration, and it will say which variable or volume it
wants. Then check the unglamorous causes: disk full
(images and volumes accumulate; `docker system df` shows
the truth), a host port the template wants already claimed
by another resource, and a template that has drifted from
its upstream (chapter 15) — in which case the project's
issue tracker usually already knows.
