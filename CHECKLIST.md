# Docker security deployment checklist

Use this before approving a new Docker workload. Record who checked each item and the date.

## Image and build
- [ ] Image publisher and source are trusted and documented.
- [ ] Base image and dependency vulnerabilities reviewed; high-impact findings addressed or formally accepted.
- [ ] Only required packages and build artifacts included.
- [ ] Image version/digest is controlled and updated on a defined cadence.
- [ ] No real secrets embedded in layers, build args, source or logs.
- [ ] CI scans dependencies, images and committed secrets.

## Runtime
- [ ] Application runs as a non-root UID.
- [ ] Rootless Engine or user namespace strategy assessed.
- [ ] No `--privileged` without an approved, documented exception.
- [ ] No Docker socket mount without a security review.
- [ ] Capabilities minimized; unnecessary host namespaces and devices unavailable.
- [ ] Default seccomp and applicable LSM profiles remain enforced.
- [ ] No-new-privileges enabled where supported.
- [ ] Filesystem read-only except explicitly necessary writable mounts.
- [ ] Memory, CPU and PID restrictions are appropriate.

## Network, data and operations
- [ ] Only necessary ports published; internal services not exposed.
- [ ] TLS, authentication, network isolation and egress controls reviewed.
- [ ] Secrets stored and delivered securely, with rotation procedures.
- [ ] Volumes, backups and host mount permissions minimized.
- [ ] Docker daemon access and `docker` group membership restricted.
- [ ] Host, Engine and dependencies receive security patches.
- [ ] Central logging/alerting in place and retention established.
- [ ] Incident containment, recovery and backup restore tested.
- [ ] Exceptions have owner, rationale and expiration date.

**Review evidence:** deployment / environment: ______  reviewer: ______  date: ______  exceptions: ______
