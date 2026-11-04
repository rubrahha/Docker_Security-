# 02 — Runtime hardening

## Security-first baseline

The following example uses a placeholder image. It is **not** guaranteed to start without adapting the application to a non-root user, read-only filesystem and writable temporary location.

```bash
docker run --rm \
  --name hardened-app \
  --user 10001:10001 \
  --read-only \
  --tmpfs /tmp:rw,noexec,nosuid,size=64m \
  --cap-drop ALL \
  --security-opt no-new-privileges:true \
  --pids-limit 128 \
  --memory 256m \
  --cpus 1 \
  --publish 127.0.0.1:8080:8080 \
  <your-image:reviewed-tag>
```

Notes:
- `--cap-drop ALL` can break workloads that legitimately need specific kernel capabilities; add back only a reviewed capability.
- Keep Docker's default seccomp profile enabled. Avoid `seccomp=unconfined`.
- Use AppArmor/SELinux where available; policy and runtime behavior vary by distribution.
- Bind to `127.0.0.1` for local access; put externally exposed services behind a controlled ingress.
- Use health checks and monitor restarts; health checks are not security controls by themselves.
- Apply CPU/memory/PID limits based on real workload measurements; constraints may be different under rootless mode and Docker Desktop.

## Avoid these dangerous settings

```bash
# Anti-patterns: DO NOT use without a carefully justified exception
docker run --privileged ...
docker run -v /var/run/docker.sock:/var/run/docker.sock ...
docker run --network host ...
docker run --pid host ...
docker run --security-opt seccomp=unconfined ...
```

Host bind mounts can expose host content. A read-only mount reduces writes but does not protect sensitive data from being read. Do not assume `--read-only` makes all volumes read-only.

## Non-root identity

Use a numeric UID/GID that can read the application's files, bind its intended port and write only to explicitly provisioned locations. Test file ownership in mounted volumes. Do not solve permission errors with `chmod -R 777`.

## Host-side isolation

Evaluate rootless Docker or userns-remap as appropriate. These reduce some daemon/container privilege risks but have operational tradeoffs and do not replace patching or monitoring.

References: [Docker security](https://docs.docker.com/engine/security/), [Rootless](https://docs.docker.com/engine/security/rootless/), [Compose services](https://docs.docker.com/reference/compose-file/services/).
