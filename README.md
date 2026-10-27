# docker-proxy

Traefik v3 reverse proxy for local Docker Compose stacks.

## Start the proxy

```sh
mise run up
```

Traefik dashboard: <http://localhost:8080>

## Add a service to the proxy

Three changes to any existing `docker-compose.yml`:

1. **Join the `proxy` network** — declare it as external and attach your service:

```yaml
services:
  myapp:
    image: ...
    networks:
      - proxy          # add this

networks:
  proxy:
    external: true     # add this block
```

2. **Enable Traefik and set the routing rule** via labels:

```yaml
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.myapp.rule=Host(`myapp.localhost`)"
      - "traefik.http.services.myapp.loadbalancer.server.port=3000"  # your app's port
```

3. Access at <http://myapp.localhost> (no port required).

> `.localhost` resolves to `127.0.0.1` on macOS/Linux without any `/etc/hosts` changes.
> Use a unique router name (e.g. `myapp`) per service to avoid conflicts.

## Full example

See [`example/docker-compose.yml`](example/docker-compose.yml) — runs `traefik/whoami` at <http://whoami.localhost>.

```sh
cd example && docker compose up -d
```

## Files

| File | Purpose |
|------|---------|
| `docker-compose.yml` | Traefik container + shared `proxy` network |
| `traefik.yml` | Static config (entrypoint, Docker provider, dashboard) |
| `mise.toml` | `up` / `down` / `logs` / `ps` task shortcuts |
| `example/` | Minimal working stack to verify the setup |
