# Projects and Environments

Outcome: you can organise real work on the ladder from
chapter 2 — what deserves its own project, what an
environment actually separates, and the small discipline
that keeps a growing dashboard findable.

## The ladder, in working clothes

Recall the shape: **Team → Project → Environment →
Resources**. In daily use:

- A **project** is a thing in the world: *the bookshop site*,
  *the club's booking system*, *my assorted bots*. Not a
  technology ("the Node project") and not a machine ("the VPS
  project") — projects outlive both.
- Inside it, an **environment** is a lane with its own
  complete set of resources. Every project starts with one
  named **production**. The resources in production — the
  application containers, the database, the services — are
  distinct objects from the identically-named resources in
  any other environment: separate containers, separate data,
  separate domains.
- A **resource** is an application, a service, or a database
  (chapters 8 and 10 create them), placed in one environment,
  on one destination (one server's Docker).

Two consequences follow, and both are features if you
expect them and traps if you do not:

1. **Nothing crosses lanes by itself.** Give the bookshop a
   *staging* environment and you have created an empty lane,
   not a copy of production. You add the resources it needs;
   their environment variables (chapter 12) are likewise set
   per environment, so staging can point at a test database
   and test payment keys — which is exactly the point.
2. **Names repeat; identity doesn't.** Two environments can
   each have an application called `web`. When something
   alerts, breaks or bills, the environment is part of its
   name. Read screens with that habit from day one.

## The second lane most people actually want

Coolify's best-known environment trick is not a hand-made
staging lane but an automatic one: **preview deployments**.
For an application connected to a git provider, a pull
request can spin up a temporary copy of the application —
its own container, its own generated subdomain — so a change
can be looked at before it merges. When the pull request
closes, the preview goes away. Under the bonnet this is
environment machinery doing what it is for: same recipe,
different lane, torn down when finished. If your work
involves reviewing changes (even your own, tomorrow), set
previews up once per application and let them happen.

## A tidy-housekeeping paragraph

Platforms rot by accumulation, and the dashboard makes
creating things so cheap that the rot is painless until it
isn't. Three habits, cheap now and expensive later:

- **One project per real thing**, even if the thing is tiny.
  The alternative — one project called *misc* — recreates the
  junk drawer with extra steps.
- **Delete previews' leftovers and dead resources** when a
  project ends. A stopped container still holds disk, a
  domain record, and a place in every list you scan.
- **Watch the destination, not just the resource.** Five
  "small" projects on the host server is a capacity plan,
  whether you wrote one or not. Chapter 3's sizing honesty
  applies per machine, and the dashboard's server view —
  fed by the sentinel agent — is where you check it.
