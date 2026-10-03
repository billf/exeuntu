# exeuntu

exeuntu is available at http://ghcr.io/boldsoftware/exeuntu

exeuntu is the default base image for [exe.dev](https://exe.dev/). It is kitted-out
for developers, based on ubuntu24.04, and includes systemd.

We believe that minimal containers make for terrible developer (and agent)
experiences, so exeuntu includes a lot of stuff, mostly from apt.

You can build exeuntu with Docker, but running it, including systemd, is
difficult with Docker.

## Installing Nix on a running VM

The supported shape for Nix on exeuntu is the Determinate installer,
pinned by version and checksum (re-pin to taste):

```sh
curl -sSfL -o /tmp/nix-installer \
  "https://github.com/DeterminateSystems/nix-installer/releases/download/v3.22.5/nix-installer-x86_64-linux"
echo "9432d40ec1d0d4ebb6284848cc389bdb1bbf7aeb5c1cea11e7efc9daf0a1ec82  /tmp/nix-installer" | sha256sum -c -
chmod +x /tmp/nix-installer
/tmp/nix-installer install linux --no-start-daemon --no-confirm
. /nix/var/nix/profiles/default/etc/profile.d/nix-daemon.sh
```

Notes:

- `--no-start-daemon` is the systemd-container shape; do not use
  `--init none` (root-only Nix) or `--prefer-upstream-nix` (unsupported).
- Builds without user namespaces (e.g. inside `docker build`) need
  per-command relaxations, never persisted config:
  `nix --option sandbox false --option build-users-group "" ...`.
- If `determinate-nixd` is present, its automatic GC can collect
  unrooted store paths on a nearly full disk; disable it via
  `/etc/determinate/config.json` when the disk is managed externally.

