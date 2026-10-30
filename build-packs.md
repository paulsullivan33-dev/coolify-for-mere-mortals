# Build Packs

Outcome: given any repository, you can pick how Coolify
should turn it into a running image — and you know which of
the menu's five entries the project itself is quietly
retiring.

A **build pack** (the creation form's term; afterwards the
same choice is listed as the application's **build
strategy**) answers one question: *the repository is text —
what makes it a program?* The documentation's overview draws
the map this chapter follows: Nixpacks, Railpack, Static and
Dockerfile each produce **one** application image; Docker
Compose can build or pull an image **per service**; and a
prebuilt **Docker Image** resource skips building altogether,
because the image already exists in a registry.

## Railpack — the successor

Railpack is Railway's newer automatic builder, adopted by
Coolify as a first-class option in v4.1: point it at a
repository and it detects the language and framework, works
out install and start commands, and produces a locked,
reproducible image. It honours command overrides and a
`railpack.json` file when detection guesses wrong. The
documentation labels it **Beta**, and the honest reading of
that label is "the future, still settling" — new projects
that suit automatic detection are the ones to try it on
first, with the Dockerfile below as the known-good escape
hatch.

## Nixpacks — the old default, retiring

Nixpacks is the automatic builder Coolify grew up with, and
a decade of tutorials assume it. It still exists in the menu
and still works — existing applications built with it keep
deploying. But this guide follows the project's own current
guidance, stated in its repository without much varnish:
**Nixpacks is deprecated; prefer the Dockerfile build pack,
or Railpack where it fits.** Nixpacks' own maintainers have
frozen it in favour of Railpack. The practical translation:
do not start new work on it, do not be surprised when a new
language version is slow to appear in it, and when an old
application needs a change anyway, let that be the day it
moves.

## Static — files in, website out

The Static build pack is the smallest idea on the menu: the
repository already contains the finished website — HTML, CSS,
JavaScript, images — and Static packages it into an Nginx
image and serves it. No build command runs, because there is
nothing to build. Two settings carry the whole chapter's
worth of mistakes: the **Base Directory** (`/` if the files
sit at the repository root, or the subfolder — `/dist` is the
classic — if they don't), and the fact that a framework site
which *generates* its files (Astro, Hugo, Next and friends)
is only "static" once those files are committed; if the
build step must run at deploy time, you want Railpack or
Nixpacks' detection instead, or generate the site yourself
and commit the output. Coolify generates a sensible default
Nginx configuration for the image, which you can view and
edit (single-page-app fallbacks are the usual reason) — note
that the configuration is baked in at build time, so changing
it means redeploying, not restarting.

## Dockerfile — the escape hatch that is often the front door

If the repository contains a `Dockerfile`, Coolify builds
exactly that. No detection, no opinions: the file is the
recipe, with all the reproducibility and all the
responsibility that implies. This is the build pack the
project recommends first in its own guidance, the one every
Docker tutorial on the internet transfers to, and the one to
choose when an automatic builder's guess differs from what
you know your program needs (a system package, an odd
runtime, a build step with opinions). The series' own
[Docker guide](https://paulsullivan33-dev.github.io/docker-for-mere-mortals/)
covers writing the file itself.

## Docker Compose — whole stacks as one application

Some programs are plural: a web app, a worker, a database of
its own. The Docker Compose strategy takes a compose file
from the repository and treats its services as one
application — each service built from its own build section
or pulled from a registry — deployed and routed together,
with Traefik labels doing the traffic direction. It is also
the strategy Coolify's one-click services (chapter 10) are
built on under the surface. If you arrive with a working
`docker-compose.yml` from local development, this is your
door; the series'
[Docker Compose guide](https://paulsullivan33-dev.github.io/docker-compose-for-mere-mortals/)
covers the format.

## Choosing, in one paragraph

Finished files only → **Static**. A repository an automatic
builder should understand → **Railpack** (accept the Beta
label knowingly). A `Dockerfile` you trust — or an automatic
guess you don't → **Dockerfile**. Several cooperating
containers defined in a compose file → **Docker Compose**.
An image that already exists → skip this chapter, create a
**Docker Image** resource. **Nixpacks** → only to keep an
existing application as it is, until its next real change.
