# Limits

Chapter 1 promised this chapter: what Coolify cannot do, or
does at a price, stated without the salesmanship. Knowing
the edges is part of operating the thing well.

## One platform, one point of failure

Coolify centralises. If its host server dies, every
application it manages is down — not merely unmanageable,
down, because the applications live on machines the platform
also runs (in the common one-server setup, the same machine).
Multi-server setups soften this for the workloads — an
application server can outlive the dashboard host — but the
control plane itself is a single instance, with no built-in
high-availability mode. Your defence is chapter 13: backups
that make rebuilding the instance a procedure, not an
archaeology project.

## Builds are heavy, and they happen at home

Automatic building is the platform's core convenience and
its sharpest resource edge. Builds run on your servers — by
default on the very server that will host the application —
and a large build can saturate a small machine and slow
everything else on it (the documentation itself warns a busy
build can make a server unresponsive). Build servers
(chapter 6) and bigger machines mitigate; they do not repeal
physics. Hosted platforms hide this with someone else's
elastic capacity; here the capacity is yours.

## It stops at Docker, deliberately

Coolify's world is containers on named servers. It has no
serverless mode, no edge network, no managed global
anycast, no autoscaling across machines it conjures from a
cloud account. An application that needs to be in twelve
regions, or to scale from zero to ten thousand users in a
minute, needs a different home — chapter 14 named them.

## The catalogue is a commons, not a contract

The 353 service templates are maintained by the project and
its community with varying attention. Templates pin image
versions; upstream projects move; a template can lag, or
encode defaults a careful operator would change. "One-click"
describes the installing, not the owning. The same applies,
in miniature, to build-pack detection: Railpack and
Nixpacks guess well and guess wrong, which is why the
Dockerfile escape hatch matters.

## You are the platform team

The limit underneath all the others. Coolify removes the
*repetitive* work of operating servers; it does not remove
the *judgement*: capacity, patching, backup discipline,
access control, and the 3 a.m. decision are staffed by you.
For a person's own projects that staffing is light and the
trade is good. The failure mode this guide most wants to
prevent is not a technical one — it is a reader standing up
a dozen services for friends and neighbours, on one
unpatched box, and discovering the staffing requirement
after the fact.

## This guide's own limit

Finally, the standing disclosure, repeated from the README:
Coolify was **not run** for this guide. It cannot be — it
needs Docker and a machine to take over, and the lab is a
shared sandbox with neither to give. The guide is built from
the project's own install script, compose files, source
layout and catalogue, read closely, plus its official
documentation for the workflows. Where a behaviour could not
be settled from those sources, the text says so or stays
general. `criteria.md` holds the ledger.
