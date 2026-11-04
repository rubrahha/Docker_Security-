# 03 — Image and supply-chain security

## Secure Dockerfile practices

1. Choose a maintained minimal base image from a trusted publisher.
2. Pin version deliberately; for stronger reproducibility pin an audited digest.
3. Use multi-stage builds to exclude compilers/build tools from runtime.
4. Install only production dependencies and avoid unnecessary packages.
5. Create and run as a non-root UID.
6. Never put passwords, access tokens or private keys in `ARG`, `ENV`, `COPY` or image layers.
7. Use BuildKit secret mounts for build-time credentials.
8. Rebuild and retest regularly as CVEs and dependency versions change.

## Practical commands

```bash
docker build --pull --tag example-app:local -f examples/Dockerfile examples
docker image inspect example-app:local
docker image history --no-trunc example-app:local
docker scout cves example-app:local
```

`docker scout` depends on Docker Scout availability and may need authentication or a subscription; alternative scanners such as Trivy or Grype can be evaluated separately. Scan OS packages **and** application dependencies. Review false positives and compensating controls; not every finding is exploitable.

## Build-time secrets

With BuildKit and a Dockerfile containing `RUN --mount=type=secret,id=... ...`:

```bash
docker build --secret id=build_token,src=./local-secret.txt -t example-app:local .
```

Only use a secret-mount consuming build step when required. Keep `local-secret.txt` outside version control and out of the build context. Avoid echoing secrets to build logs or copying them to later layers.

## Integrity and provenance

- Validate image ownership and expected digest before promotion.
- Prefer provenance attestations and signed images where your registry and build workflow support them.
- Generate and retain an SBOM and build metadata.
- Retest and repin on updates. **Digest pinning without updates leaves you on an old vulnerable build.**
- Protect CI secrets and restrict who can publish production image tags.

References: [Docker build best practices](https://docs.docker.com/build/building/best-practices/), [Docker build secrets](https://docs.docker.com/build/building/secrets/), [Docker Scout](https://docs.docker.com/scout/).
