# syntax=docker/dockerfile:1
# A tiny educational image illustrating non-root execution.
# For production: choose an approved base and pin a reviewed digest.
FROM python:3.13-alpine
WORKDIR /app
RUN addgroup -S appgroup -g 10001 && adduser -S appuser -u 10001 -G appgroup
COPY --chown=10001:10001 app.py /app/app.py
USER 10001:10001
EXPOSE 8080
CMD ["python", "-B", "/app/app.py"]
