# The Neighbours

Coolify sits in a crowded street. This chapter names the
houses on either side fairly — what each does that Coolify
doesn't, and what Coolify does that they don't — so you can
tell whether you are in the right one. The neighbours
themselves are characterised here, not run; this guide's lab
dissected Coolify only.

## The hosted platforms: Heroku, Vercel, Netlify, Railway

The originals. **Heroku** invented the shape Coolify copies:
git push, buildpacks, add-ons, a dashboard. **Vercel** and
**Netlify** refined it for frontend and serverless work,
with global edge networks no single VPS can imitate.
**Railway** is the modern generalist — and, fittingly, the
birthplace of Railpack, chapter 9's builder.

What they do that Coolify doesn't: they own the machines.
Patching, hardening, DDoS absorption, hardware failure,
capacity — all theirs, at 3 a.m. as at noon. Their platforms
scale a successful application further than one or two of
your servers will, and their bills scale with it: per-app,
per-seat, per-gigabyte pricing that punishes exactly the
long tail of small projects Coolify makes free at the margin.

Choose them when the application matters more than the
money, or when nobody in the house wants to be a platform
operator. Choose Coolify when the projects are yours, the
budget is a server you already have, and you accept chapter
13's paragraph as your own job description.

## Dokploy and the other self-hosted panels

Coolify is not the only open-source answer. **Dokploy** is
its closest neighbour — same one-line install shape, same
Traefik-and-Docker heart, a somewhat different dashboard
philosophy, and Docker Swarm features for multi-server
setups. **CapRover** is an older, smaller take on the same
idea. Honest guidance: the overlap is large, the differences
are taste and trajectory more than capability, and anyone
choosing between them should look at which project's
documentation and community feel like home. This guide
teaches Coolify because its catalogue and community are the
largest of the group — not because the neighbours are
unworthy.

## Plain Docker Compose, by hand

Everything Coolify runs is Docker containers, and nothing
stops you writing the compose files yourself — the series'
[Docker Compose guide](https://paulsullivan33-dev.github.io/docker-compose-for-mere-mortals/)
teaches exactly that. By hand you gain total transparency:
every line of configuration is yours, in git, reviewable.
You lose the platform's connective tissue: no catalogue, no
git-push redeploys, no certificate automation unless you wire
Traefik (or another proxy) yourself, no dashboard tying five
projects together. Many experienced self-hosters run a hybrid
— their critical one or two services by hand, the long tail
in Coolify — and this guide considers that a sign of taste,
not indecision.

## Managed Kubernetes and the big clouds

At the far end of the street stand Kubernetes and the cloud
platforms' own container services. They solve problems
Coolify does not attempt: dozens of machines, automated
scheduling and healing, teams of teams. Their cost is an
order of magnitude more concepts, and a part-time job in
themselves. If your honest scale is "some servers I can
name," they are the wrong tool however impressive the demo.
If the day comes when it isn't, Coolify will have taught you
the portable half of the ideas — images, environments,
variables, domains — on friendlier ground.
