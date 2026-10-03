# exeuntu

exeuntu is available at http://ghcr.io/boldsoftware/exeuntu

exeuntu is the default base image for [exe.dev](https://exe.dev/). It is kitted-out
for developers, based on ubuntu24.04, and includes systemd.

We believe that minimal containers make for terrible developer (and agent)
experiences, so exeuntu includes a lot of stuff, mostly from apt.

You can build exeuntu with Docker, but running it, including systemd, is
difficult with Docker.

## SSH bypasses PAM; deliver PATH without pam_env

Non-interactive SSH on the exe.dev platform never runs the VM's PAM
stack, so `pam_env` (including `/etc/environment` handling) and
`pam_exec` hooks are inert there. They still apply under stock sshd
elsewhere, so existing entries are harmless to keep -- but anything
load-bearing (notably PATH for agent CLIs and Nix profile bins) must be
delivered another way:

- Bake symlinks into `/usr/local/bin` (never replacing shipped names),
  which stays first on PATH.
- Or set PATH in the managed shell profile (`.bashrc`/`.profile`).
- Order session gating on systemd unit ordering
  (e.g. activation `Before=ssh.service`), not on a PAM hook.

Same root cause as the `XDG_RUNTIME_DIR` workaround in the Dockerfile:
those variables are likewise absent over non-interactive SSH and must be
set explicitly (e.g. `systemctl --user` fails with "No medium found"
without it).

