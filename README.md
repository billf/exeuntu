# exeuntu

exeuntu is available at http://ghcr.io/boldsoftware/exeuntu

exeuntu is the default base image for [exe.dev](https://exe.dev/). It is kitted-out
for developers, based on ubuntu24.04, and includes systemd.

We believe that minimal containers make for terrible developer (and agent)
experiences, so exeuntu includes a lot of stuff, mostly from apt.

You can build exeuntu with Docker, but running it, including systemd, is
difficult with Docker.

## Private image pulls without VM-side credentials

Pull indirection keeps tokens off the VM:

- Base images: pull through the platform proxy host
  (`ghcr.int.exe.xyz/<owner>/<image>:<tag>`). Auth is handled
  proxy-side; the VM never sees the token.
- Experiment images: run an authenticated `registry:2` on a disposable
  VM (`REGISTRY_AUTH=htpasswd`), make that VM's HTTPS proxy public, and
  create experiment VMs with
  `--image <registry-vm>.exe.xyz/<repo>:<tag> --registry-auth user:pass`.
  Note the reference uses the HTTPS hostname with no `:8000` suffix.
  Unauthenticated requests get 401 from the registry itself. Set the
  proxy back to private (or delete the registry VM) afterwards.

