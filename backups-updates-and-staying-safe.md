# Backups, Updates and Staying Safe

Outcome: you leave this chapter with a backup habit that
covers the three places your digital life now sits, a
deliberate choice about updates instead of a default you
never noticed, and the security picture stated in one place,
whole.

## Backups: three things, not one

"Back up Coolify" is ambiguous until you split it:

1. **Your data.** The volumes and folders your applications
   and services write to — under `/data/coolify/` on each
   server (`applications`, `services`, and friends) plus the
   Docker volumes. This is the irreplaceable part: your
   photos in Immich, your documents in Paperless.
2. **Your databases.** Database resources (chapter 10) can be
   backed up on a **schedule** from the dashboard, with the
   dumps sent to **S3-compatible storage** — a bucket at a
   provider, or a MinIO elsewhere — and restored from the
   same screens. A scheduled database backup that lands on
   the same disk as the database is a copy, not a backup;
   point it off the machine.
3. **The platform itself.** Coolify's memory is its own
   PostgreSQL database plus `/data/coolify/source/.env` with
   its generated secrets. The installer told you to copy that
   `.env` somewhere safe the day you installed; this chapter
   upgrades the advice to a standing arrangement: keep an
   off-server copy of the `.env`, and of the whole
   `/data/coolify/source/` folder, refreshed whenever you
   change instance settings. With those and your data, a new
   server can be rebuilt into *your* Coolify. Without them,
   you are re-creating accounts and re-entering every setting
   from memory.

The test of a backup is a restore you have actually done.
Pick the least important real thing you run, and rehearse
restoring it once. The afternoon this costs is the cheapest
tuition in this guide.

## Updates: two moving things

**Coolify itself.** The installer leaves auto-update on:
the platform checks for and applies new versions by itself
(the `AUTOUPDATE` setting from chapter 4; the machinery is
the `upgrade.sh` script the installer placed in
`/data/coolify/source/`). The honest weighing:

- *For* auto-update: this is an internet-facing control
  panel; its security fixes matter, and they only protect
  you once applied. Most self-hosters, most of the time,
  should let it update.
- *Against:* an update you did not schedule can change
  behaviour you depended on, at a time you did not choose.
  If your setup grows fragile or important, turn auto-update
  off **and replace it with a habit** — checking for updates
  yourself on a rhythm, reading the release notes, pressing
  the button. Off-with-a-habit is a position; off-by-default
  is how panels stay unpatched for a year.

**Your applications** update when you redeploy them — a new
image is built from the same recipe. For services, the
template's image tags decide what "current" means; review
them occasionally rather than assuming the catalogue follows
upstream releases for you. And the server's own operating
system and Docker still want their ordinary patching; the
platform does not absorb that job.

## Staying safe: the paragraph, whole

Coolify concentrates power, by design: its dashboard can run
commands as root on every server it manages, over SSH keys it
holds. Everything else in this chapter is commentary on that
sentence. The working rules:

- **Win the registration race** (chapter 4), or pre-create
  the root account so registration is never open. Then
  confirm public registration is closed.
- **Give the dashboard its HTTPS domain** (chapter 11) and
  close port 8000 to the world in your firewall afterwards.
  Prefer a strong, unique password on the root account; it
  is a root password.
- **Treat team membership as root-by-proxy** (chapters 5 and
  12): owners and admins can deploy arbitrary containers —
  which is root on the server — and read application secrets.
  Add people at the lowest role that works.
- **Guard the keys.** Coolify's SSH keys and the instance
  `.env` are the crown jewels; the backups of them (which you
  need) belong in a password manager or an encrypted store,
  not in a documents folder that syncs to three clouds.
- **Keep the whole stack patched**: Coolify (above), the OS,
  and Docker. An up-to-date Coolify on an unpatched server is
  a locked door in an open house.
- **Expose databases never, applications deliberately** —
  chapter 10's rule — and open firewall ports (chapter 3)
  only for what you actually run.

None of this is a reason not to run Coolify. It is the
ordinary price of operating a platform, itemised — and a
person who pays it knowingly is exactly who this guide was
written for.
