# Servers — This Machine and Others

Outcome: you understand what it means, mechanically, for
Coolify to "manage" a server, why its own host is simply the
first entry on the list, and how to judge whether adding
another machine helps you or just spreads your risk around.

## The first server is the host itself

When the installer finished, Coolify already had one server:
its own. The dashboard's server list shows it (the project's
own code calls it the *localhost* server), and everything in
chapters 8 and 10 can deploy onto it without any further
setup. Chapter 4's last steps explain why it works: the
installer generated an SSH key on the host and added it to
root's `authorized_keys`, so Coolify manages its home machine
by exactly the mechanism it uses for every other server — it
SSHes in, as root, and drives Docker.

That symmetry is the design's elegance and its warning label
in one. There is no privileged back door: the platform's
power over every machine it touches is an SSH key, and
whoever holds Coolify holds the keys to all of them.

## Adding a server

Adding a machine — a second VPS, a box at home — follows the
same pattern, walked in the Servers section of the dashboard:

1. **Prepare the machine.** A supported Linux, Docker
   installed (Coolify can work with a Docker you installed
   yourself; the server's own install flow expects Docker 24
   or newer, as chapter 4's gate suggests), and an SSH server
   running. The project's knowledge base has a page on
   OpenSSH setup — the installer itself links to it
   (`coolify.io/docs/knowledge-base/server/openssh`) when it
   finds root SSH login disabled.
2. **Give Coolify a way in.** You provide the server's address
   and either let Coolify generate a fresh SSH key for it —
   you then add the public half to the target's
   `authorized_keys` — or supply a key the machine already
   trusts. The login user can be root or a sudo-capable user;
   the project's code is careful to route commands through
   sudo parsing for non-root users, but root is the
   well-trodden path.
3. **Validate.** Coolify connects, checks Docker, and reports
   the machine ready. On validation it sets the server up as
   a destination: its own Docker network, and the Traefik
   proxy of chapter 2 started as a container, claiming ports
   80 and 443 on that machine.

From then on the server appears wherever a resource asks
"where should this run?" — its proxy routes its domains, its
Docker runs its containers, and the sentinel agent reports
its vital signs back to the dashboard.

## Build servers: separating the heavy lifting

One special role deserves its own paragraph. Building an
image — compiling, bundling, resolving dependencies — is the
spikiest work a deployment does, and by default it happens
**on the same server that will run the application** (the
documentation's builds overview is explicit about this). On a
small VPS, a build can starve the very applications the
server is hosting; chapter 3 quoted the docs' warning that a
busy build can make a server unresponsive. Coolify lets you
designate a **build server**: a machine that only builds,
pushing the finished image to a registry the deployment
server pulls from. A build server runs no applications of its
own. For a one-person fleet this is a refinement, not a
requirement — but it is the correct first answer when deploys
start making your sites wobble.

## How many servers should you have?

Honest guidance, in ascending order of fuss:

- **One** (the host) is the right number for most readers of
  this guide. Everything in it works there.
- **Two** earns its keep when you want a wall between the
  platform and the workloads (Coolify's host runs only
  Coolify; everything else lives elsewhere), or a machine in
  a different place — a home server beside a rented VPS is a
  common, happy shape.
- **Many** is where Coolify starts competing with tools
  built for fleets, and where its simplicity stops paying.
  The neighbours chapter (14) is fair about that boundary.
