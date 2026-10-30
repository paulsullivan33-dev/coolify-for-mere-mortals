# Domains and Automatic HTTPS

Outcome: your application answers on a name you own, over
HTTPS, with a certificate that renews itself — and you can
explain the chain of three things (DNS, proxy, certificate)
that must each agree before the padlock appears.

## The chain, link by link

Chapter 2 introduced the cast; here they perform.

1. **DNS.** You create a record — typically an `A` record for
   `app.example.com` — pointing at your server's public IP
   address. Until the world's resolvers agree the name means
   your machine, nothing downstream can work, and no error
   message in Coolify will ever say "your DNS is wrong" in so
   many words. Check this link first, every time.
2. **The proxy.** Traefik, running on that server, owns ports
   80 and 443. When a request for `app.example.com` arrives,
   Traefik must know which container wants it. Coolify writes
   that knowledge onto the application container as labels
   when it deploys — the hostname, which entrypoint (web or
   websecure), and which internal port to hand the request
   to.
3. **The certificate.** For the HTTPS entrypoint, Traefik
   asks Let's Encrypt to vouch for the name — which it does
   by checking that the name really resolves to the machine
   asking (the port-80 challenge is the usual path — one more
   reason port 80 stays open even for sites that "only use
   HTTPS"). The certificate lands in the proxy's storage
   under `/data/coolify/proxy`, and Traefik renews it
   automatically before it expires.

## Telling Coolify the name

Each application (and service) has a **Domains** setting. The
documentation's shape of it: you add a domain as a full URL —
protocol, hostname, and optionally a port or path, as in
`https://app.example.com`. The protocol is a real choice, not
decoration:

- `http://…` routes the name on port 80 only.
- `https://…` routes it on 443 *and* redirects the plain-HTTP
  version to HTTPS — the setting most public sites want.

Add each additional hostname separately (the bare domain and
its `www` twin are two entries). Then **redeploy**: the
labels live on the container, so a domain change is a
deployment change, and the first request afterwards is when
the certificate gets requested. A freshly pointed domain can
take a few minutes to earn its padlock while DNS propagates
and Let's Encrypt answers; chapter 16 covers what it looks
like when one of the three links is actually broken.

## The dashboard's own name

Everything above applies to Coolify itself. Giving the
instance a domain in its settings moves the dashboard from
`http://<ip>:8000` to a proxied `https://coolify.example.com`,
and at that point a sensible hardening becomes available:
close port 8000 in the firewall to the outside world, since
you no longer need it. Do the steps in that order — domain
first, confirm HTTPS works, *then* close the port — and
chapter 13 will remind you why this small ceremony matters:
the dashboard is a root shell with a login page, and it
deserves the protected route in.

## Wildcards and other grown-up arrangements

One certificate per name is the default story. Traefik also
supports wildcard certificates (`*.example.com`) through a
DNS challenge with your DNS provider, which suits preview
deployments (chapter 7's generated subdomains) and large
families of names. It needs an API token for your DNS
provider stored in the proxy configuration — treat that
token as what it is, a key to every name you own, and prefer
the per-name route until you genuinely feel the friction.
