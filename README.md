# Docker Security Handbook 🐳🔐

A practical, defensive guide to building, running, auditing, and maintaining secure Docker containers. Written for developers, DevOps engineers, and security learners.

> **Scope:** Docker Engine and Docker Compose. Examples primarily target Linux containers; Docker Desktop on Windows/macOS uses a Linux VM, so host-specific commands may differ. This repository is educational, not a guarantee of compliance or breach prevention.

## Start here

| Goal | Guide |
| --- | --- |
| Learn the attack surface and core controls | [Docker security fundamentals](docs/01-fundamentals.md) |
| Harden a container at runtime | [Runtime hardening](docs/02-runtime-hardening.md) |
| Build safer images | [Image and supply-chain security](docs/03-image-security.md) |
| Protect ports, networking and secrets | [Network, secrets and host security](docs/04-network-secrets-host.md) |
| Detect issues and respond | [Auditing and incident response](docs/05-audit-incident-response.md) |
| Practice with a baseline | [Secure Compose example](examples/compose.yaml) |
| Review deployments | [Security checklist](CHECKLIST.md) |

## Threat model in 60 seconds

Treat untrusted images, compromised dependencies, vulnerable application code, exposed Docker APIs, over-privileged containers, secrets in layers/logs, and writable host mounts as key risks. A container is **not** a hard security boundary equivalent to a separate VM: the Linux kernel is shared.

Defense in depth:
1. Keep the host, Docker Engine, Compose, runtime and base images updated.
2. Trust and verify image sources, and scan images and dependencies.
3. Prefer rootless Docker where suitable; always run application processes as a non-root user.
4. Drop unnecessary capabilities; avoid `--privileged`, host namespaces and Docker socket mounts.
5. Use `no-new-privileges`, a read-only root filesystem where feasible, and resource limits.
6. Keep default seccomp and suitable AppArmor/SELinux confinement enabled.
7. Limit exposed ports, outbound access and lateral movement.
8. Inject secrets at runtime through a managed secret mechanism, not build args or image layers.
9. Audit configuration, monitor events and logs, and regularly test restore procedures.

## Quick commands

```bash
docker version
docker info
docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Ports}}'
docker inspect <container-name>
docker image history --no-trunc <image-name>
docker events --since 1h
docker compose -f examples/compose.yaml config
```

The example is intentionally restrictive. You need to replace its placeholder application image with one that supports a non-root UID, listens on port 8080 and can run with a read-only root filesystem. See its inline notes before deployment.

## Repository layout

```text
Docker_Security-/
├── README.md
├── CHECKLIST.md
├── SECURITY.md
├── docs/
│   ├── 01-fundamentals.md
│   ├── 02-runtime-hardening.md
│   ├── 03-image-security.md
│   ├── 04-network-secrets-host.md
│   └── 05-audit-incident-response.md
└── examples/
    ├── compose.yaml
    ├── Dockerfile
    └── .dockerignore
```

## Authoritative references

- [Docker Engine security](https://docs.docker.com/engine/security/)
- [Rootless Docker](https://docs.docker.com/engine/security/rootless/)
- [User namespace remapping](https://docs.docker.com/engine/security/userns-remap/)
- [Docker build best practices](https://docs.docker.com/build/building/best-practices/)
- [Compose service reference](https://docs.docker.com/reference/compose-file/services/)
- [Compose trust model](https://docs.docker.com/compose/trust-model/)
- [OWASP Docker Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html)

## Responsible use

All commands are for systems you own or are authorized to inspect. Review and adapt examples to your workloads. Contributions and corrections are welcome.
