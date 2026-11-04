# 04 — Network, secrets and host security

## Networking

- Expose only the ports users need.
- For a local-only service, publish `127.0.0.1:8080:8080` rather than `8080:8080` on all interfaces.
- Separate frontend, application and database networks where necessary; do not publish database ports by default.
- Remember Docker bridge separation is not a complete microsegmentation or egress policy. Set firewall/egress controls on the host or orchestrator.
- Add authentication and TLS at a reverse proxy or application boundary as appropriate.

Check current published ports:

```bash
docker ps --format 'table {{.Names}}\t{{.Ports}}'
docker network ls
docker network inspect <network-name>
```

## Secrets

Never commit a real `.env`, private key or token. Do not place secrets in Dockerfiles or image build arguments. Docker Compose `secrets` can expose secret content as mounted files to selected services; its local-file handling is **not the same as a fully managed secret vault**. Protect host secret files and backups, restrict access, and rotate credentials after exposure.

Example local development setup:

```yaml
services:
  app:
    image: your-reviewed-image:tag
    secrets:
      - app_password
secrets:
  app_password:
    file: ./secrets/app_password.txt
```

The container reads the mounted secret under `/run/secrets/app_password` by default, if supported by the image. Keep `secrets/` ignored by Git. Avoid putting secret values directly in environment variables when a mounted secret or secret manager is available.

## Host security

- Apply OS and Docker Engine security updates.
- Restrict membership in the `docker` group; for rootful Docker it effectively grants high host privilege.
- Never expose the Docker daemon API without a strong, reviewed access-control design; prefer SSH contexts or mutually authenticated TLS for remote administration.
- Restrict access to Docker data directories, volumes, backups, and CI runners.
- Enforce firewall rules while considering Docker's networking behavior and the `DOCKER-USER` chain on supported iptables-based setups.
- Protect logs against credential leakage and use retention policies.

References: [Docker daemon attack surface](https://docs.docker.com/engine/security/), [Compose secrets](https://docs.docker.com/compose/how-tos/use-secrets/).
