# 05 — Auditing and incident response

## Daily/weekly review

```bash
docker ps --all
docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Ports}}'
docker events --since 24h
docker system df
docker inspect <container-name>
docker image history --no-trunc <image-name>
```

Review: new containers, unexpected images, published ports, restart loops, unusual network behavior, bind mounts, privileged flags, and failed authorization.

For CI/CD, require image scanning, secret scanning, dependency review, pinned actions, and restricted publishing rights. Use an SBOM and track exceptions with owners and expiry dates.

## Incident response: suspected compromised container

1. Record time, host, container ID, image digest, owner and observed indicators.
2. Contain network access using approved operational procedures. **Avoid deleting or rebuilding first** if evidence must be preserved.
3. Preserve logs, container metadata and relevant host telemetry using an authorized forensic process. Logs and `docker inspect` alone are not a full forensic image.
4. Determine whether secrets, volumes, the Docker socket, or host mounts were reachable.
5. Rotate potentially exposed credentials and revoke tokens.
6. Rebuild from trusted, updated images; do not trust a compromised running container as the recovery source.
7. Validate the root cause, patch exposure and test before restoring traffic.
8. Document incident timeline and improvements.

Do not execute suspicious files from an unknown container on your personal workstation. Follow your organization's evidence handling and retention requirements.

## Investigation commands

```bash
docker logs --timestamps --since 24h <container>
docker inspect <container>
docker top <container>
docker stats --no-stream
docker diff <container>
```

`docker diff` shows changes in the writable container layer, not writes to volumes or all possible persistence. An attacker may tamper with in-container logs; prioritize independently collected host/security telemetry.

## Further reading

- [Docker Engine security](https://docs.docker.com/engine/security/)
- [OWASP Docker Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html)
