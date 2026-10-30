# Installing Coolify

The outcome of this chapter: you can run the famous one line
knowing — specifically, not vaguely — what it is about to do
to your server, which of its changes are one-way doors, and
what to do in the ten minutes after it finishes. The
installation itself is documented-only in this guide's lab
(the sandbox has no Docker and is shared; the dissection
notes in `coolify-lab/` explain), but the script was read in
full, and everything below is what it actually does, in its
own order.

## The one line

On a fresh server, logged in as root, the documented install
is:

```bash
curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash
```

That downloads the project's install script from its CDN and
runs it as root. Two habits are worth forming before you
press Enter on any machine you care about. First, you can
download the same file and *read it first* — it is 1,179
lines of commented shell, and this chapter is a guided tour
of it. Second, you can pin a version by passing it as an
argument (`… | bash -s 4.5.4` installs that release instead of
the newest; 4.5.4 was current when this guide was written).
The script's own header carries a warning worth respecting:
"Do not modify this file. You will lose the ability to install
and auto-update."

The script refuses to run as a non-root user and exits if
your operating system is not on its supported list. Then it
works through nine numbered steps.

## The nine steps, honestly

1. **Required packages.** Installs `curl`, `wget`, `git`,
   `jq` and `openssl` with your distribution's package
   manager, skipping the step if all five are already there.
2. **OpenSSH check.** Looks for a running SSH server. If it
   cannot find one, it **installs and enables OpenSSH server
   itself**. It checks whether root login by SSH is permitted
   and warns if not. It also refuses to continue if Docker
   was installed via snap, which Coolify does not support.
3. **Docker Engine.** If Docker is missing, installs it —
   normally by piping Docker's own `get.docker.com` script
   into a shell, with a manual package-repository route as
   fallback. If Docker is present but its server is older
   than **version 24**, the install aborts with an error:
   upgrade Docker first.
4. **Docker daemon configuration.** Writes settings into
   `/etc/docker/daemon.json`: log rotation (each container's
   log capped at 10 MB × 3 files) and a default network
   address pool (10.0.0.0/8, cut into /24 networks). An
   existing daemon.json is backed up as
   `daemon.json.original-<date>` first, and other settings in
   it are preserved. Note the care here, because it cuts both
   ways: applying these settings needs a Docker restart,
   which stops every running container — so if containers are
   already running, the script *skips* the change and warns,
   rather than dropping your workloads.
5. **Downloads.** Fetches Coolify's compose files
   (`docker-compose.yml`, `docker-compose.prod.yml`), the
   production environment template, and the upgrade scripts
   from the CDN into `/data/coolify/source/`.
6. **Environment and secrets.** Creates
   `/data/coolify/source/.env` and fills its empty secret
   slots with fresh random values from openssl: the app ID
   and key, the PostgreSQL password, the Redis password, and
   the realtime (Pusher-compatible) credentials. These
   generated secrets are why chapter 12 calls this file a
   crown jewel.
7. **Its own SSH key.** Creates the `/data/coolify/` directory
   tree (applications, databases, backups, services, proxy,
   sentinel, images, ssh — owned by Coolify's user, mode 700),
   generates an ed25519 SSH key, and **appends the public half
   to root's `~/.ssh/authorized_keys`**. This is how Coolify
   will manage its own host — over SSH, as root, exactly as
   it manages any other server. It is the single most
   consequential line in the script, and it is also simply
   how the design works: chapter 6 picks this thread up.
8. **Pulling images.** Runs the upgrade script, which uses a
   short-lived `coolify-helper` container to pull the images
   and bring the stack up with Docker Compose.
9. **Starting Coolify.** Waits for the stack, checks the
   `coolify` container's health, and prints your addresses:
   `http://<public-ip>:8000` and the private ones.

## The ten minutes after

The script's final screen carries two warnings of its own,
and both deserve to be taken at full strength:

- **"Create the root account within 10 minutes."** On a
  fresh install, whoever opens the dashboard first and
  registers becomes the instance's root administrator. The
  installer deliberately leaves a short window and then locks
  registration; if you miss it, the message tells you the
  recovery (`docker restart coolify` reopens the window).
  Treat this as a race you intend to win: install, then
  immediately register, before you make tea. Better still,
  skip the race entirely — the documentation's *Root user*
  option lets you pre-create the account by passing
  `ROOT_USERNAME`, `ROOT_USER_EMAIL` and `ROOT_USER_PASSWORD`
  as environment variables to the installer, "so public
  registration is never exposed" (the password needs at least
  8 characters with upper, lower, number and symbol).
- **"Back up `/data/coolify/source/.env` to a safe place
  outside this server."** The installer's words, and chapter
  13 repeats them: that file holds the keys to the whole
  instance.

One more decision hides in step 6's neighbourhood: by
default Coolify **updates itself automatically** (nightly).
Setting `AUTOUPDATE=false` in the install environment opts
out. Neither answer is wrong; chapter 13 weighs them. What
is wrong is not knowing the default exists.

## Why "one-way door" is the right frame

Nothing here is malicious, and most of it is careful — the
daemon.json backup and the refusal to restart a busy Docker
are genuinely considerate touches. But taken together the
script re-roots a machine around Coolify: Docker installed
and configured, an SSH server guaranteed present, a new root
SSH key, a directory empire under `/data/`, and ports 80, 443
and 8000 claimed by the stack and its proxy. Uninstalling is
possible (you would remove the containers, the `/data/coolify`
tree, the key, and Docker if you installed it for this), but
nobody would call it tidy. That is the real argument for the
documentation's fresh-server advice in chapter 3: install
Coolify on a machine whose whole job is Coolify, and the
one-way door costs you nothing.
