# Before You Install

This chapter is the shopping list, and it delivers one
outcome: by the end you can say whether a particular machine
is a good Coolify host, and what else — a domain, some open
ports — it needs around it. Two different authorities speak
here, and this guide keeps them separate on purpose: the
**documentation's requirements** (what the project asks for)
and the **install script's own checks** (what the program
actually tests before it proceeds, read from the script
itself). They are not the same numbers, and the gap between
them is worth knowing about before, not after.

## The machine

Coolify runs on a Linux server you can reach by SSH:

- a **VPS** from any provider is the usual choice;
- a **dedicated server**, or a virtual machine on your own
  hardware, works the same way;
- a **64-bit Raspberry Pi** is supported;
- a spare **laptop** is on the documentation's list too —
  anything Linux with SSH access qualifies in principle.

The documentation recommends a **fresh server**. That advice
is practical, not fussy: the installer will install and
reconfigure Docker, claim the ports a web platform needs, and
install an SSH server if none exists. On a machine already
doing other work, those changes can collide with it — chapter
4 itemises them. A fresh, disposable-feeling server is the
right first home.

**Supported systems**, per the documentation: Debian and
Ubuntu (Ubuntu's non-LTS releases are steered to the manual
install), the Red Hat family (CentOS, Fedora, RHEL, AlmaLinux,
Rocky and relatives), SLES and openSUSE, Arch Linux, Alpine
Linux, and 64-bit Raspberry Pi OS. Linux only — there is no
Windows or macOS version of the server side.

**Architecture:** amd64 or arm64. That covers essentially
every VPS and any recent Pi.

## The sizes: asked for, versus checked

The documentation's minimums for Coolify itself:

| Item | Documentation minimum |
| --- | --- |
| CPU | 2 cores |
| Memory | 2 GB RAM |
| Disk | 10 GB free space |
| Architecture | amd64 or arm64 |

The docs add, honestly, that Coolify *can* run on less — one
core and 512 MB is their example — but that it is not
recommended, and that a server also running builds needs
watching: a big build on a small machine can make the whole
server, dashboard included, unresponsive.

Now the script. Reading `scripts/install.sh` end to end, the
only size it actually tests is the disk, and it tests it
**more strictly than the docs ask, but only as a warning**:

- total disk under **30 GB**, or free disk under **20 GB** →
  it prints a warning, waits five seconds, and carries on.

Its hard gates are elsewhere: it refuses to run as a non-root
user, refuses an unsupported operating system, refuses a
snap-installed Docker, and aborts if the Docker it finds (or
installs) is older than version 24. Nothing in the script
counts your CPU cores or your RAM.

Read the two together and the practical floor writes itself:
give it the documented 2 cores and 2 GB as a *minimum*, and
much more disk than 10 GB if you can — Docker images, build
caches and backups all eat disk steadily, and the script's
own 30 GB total / 20 GB free warning line is the better guide
to a comfortable machine. Everything above the floor is for
your workloads, not Coolify: the server you need is the one
that fits the applications you plan to deploy, plus this
platform on top.

## Around the machine

Three things to arrange before chapter 4 makes sense:

1. **SSH access as root (or with sudo).** The installer runs
   as root, and afterwards Coolify itself manages the machine
   over SSH. Have your login working from your own computer
   first, and try it twice — the installer can install an SSH
   server if one is missing, but a server you cannot log into
   is a bad start either way.
2. **Ports.** The firewall (yours and your provider's) needs
   to let the world reach the server on **80 and 443** (web
   traffic, and the certificate machinery uses port 80), and
   you need **22** (SSH) and **8000** (the dashboard) at least
   from where you sit. Chapter 13 argues for narrowing 8000
   down once you have given the dashboard a domain of its own.
3. **A domain name you control.** Not strictly required —
   Coolify works on bare IP addresses, and the installer
   finishes by printing `http://<ip>:8000` — but automatic
   HTTPS (chapter 11) needs domain names whose DNS records
   you can point at the server. One domain is enough; subdomains
   do the rest.
