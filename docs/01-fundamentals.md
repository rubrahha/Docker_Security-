# 01 — Docker security fundamentals

## What Docker isolates

Docker uses Linux namespaces for process/network/mount isolation, cgroups for resource management, and kernel security controls such as capabilities, seccomp, AppArmor or SELinux. Containers normally share the host kernel; a container escape or Docker daemon compromise can affect the host.

## Main security risks

| Risk | Why it matters | Primary defense |
| --- | --- | --- |
| Docker socket exposure | Access to a rootful daemon often enables host-level control | Never mount `/var/run/docker.sock` into untrusted containers |
| Privileged containers | Broad device and kernel-level access | Avoid `--privileged`; add only required capabilities/devices |
| Root inside a container | Increases consequences of misconfiguration or escape | Set `USER`; consider rootless Engine/user namespaces |
| Vulnerable images | Packages and libraries may contain known CVEs | Use trusted maintained images, scan, rebuild, patch |
| Exposed services | Unnecessary attack surface | Publish only required ports; bind admin interfaces to localhost |
| Image secrets | Secrets may remain in layers/history | Use BuildKit secrets and runtime secret management |
| Resource exhaustion | Container can consume host resources | Set memory, CPU and PID constraints |

## Docker architecture

- **Client:** `docker` CLI/API caller.
- **Daemon:** `dockerd` manages images, containers, networks and volumes. Protect its API.
- **Runtime:** containerd/runc and kernel isolation mechanisms.
- **Registry:** image distribution; treat registries and publishers as a supply-chain trust decision.

## Key distinctions

- **Non-root process:** `USER 10001` reduces in-container privilege; it does **not** turn a rootful Docker daemon into a rootless daemon.
- **User namespace remapping:** maps container IDs to different host IDs; not equivalent to rootless Docker.
- **Rootless Docker:** daemon and containers run without root privileges where supported; check compatibility with networking, storage and organizational needs.
- **Image scan:** finding known issues is useful but does not prove runtime safety.
- **Digest pinning:** pins exact image content but does not automatically deliver security fixes. Update intentionally.

## Safe inspection lab

On a machine you control:

```bash
docker version
docker info
docker ps --all
docker inspect <container>
docker image inspect <image>
docker image history --no-trunc <image>
```

Look for `HostConfig.Privileged`, `HostConfig.Binds`, published ports, `Config.User`, capability additions, `SecurityOpt`, and `ReadonlyRootfs`. Treat runtime and image configuration as separate layers.
