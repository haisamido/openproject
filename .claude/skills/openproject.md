# OpenProject Docker Skill

Manage the OpenProject Docker container using Taskfile and Docker Compose.

## Project Layout

- `compose.yaml` — Docker Compose definition (single all-in-one container)
- `Taskfile.yml` — Task runner commands for Docker Compose orchestration
- `.gitignore` — Excludes `pgdata/` and `assets/` from version control
- `pgdata/` — PostgreSQL data (bind mount, persistent)
- `assets/` — Uploaded files and attachments (bind mount, persistent)

## Container Details

- **Image:** `openproject/openproject:17` (all-in-one: app + database + memcached + worker)
- **Port:** `127.0.0.1:16969` → container port `80`
- **Network:** `openproject` (bridge)
- **Healthcheck:** `GET /health_checks/default` (30s interval, 120s start period)
- **Default credentials:** `admin` / `admin` (password change required on first login)

## Environment Variables

| Variable | Purpose |
|---|---|
| `SECRET_KEY_BASE` | Rails secret key |
| `OPENPROJECT_HOST__NAME` | Must match the exposed host:port (e.g. `localhost:16969`) |
| `OPENPROJECT_HTTPS` | Set to `false` for local dev |
| `OPENPROJECT_DEFAULT__LANGUAGE` | UI language |

**Important:** `OPENPROJECT_HOST__NAME` must always match the host port in `ports:`. Mismatch causes "Invalid host_name configuration" error.

## Task Commands

| Command | Action |
|---|---|
| `task up` | Start container (detached) |
| `task down` | Stop and remove container |
| `task start` / `task stop` | Start/stop without removing |
| `task restart` | Restart container |
| `task logs` | Follow container logs |
| `task ps` | Show container status and health |
| `task shell` | Open bash shell inside container |
| `task pull` | Pull latest image |
| `task destroy` | Stop and remove container **and volumes** (data loss) |

## Troubleshooting

- **"Invalid host_name configuration"**: Ensure `OPENPROJECT_HOST__NAME` port matches the exposed port in `ports:`. If persists after fixing, `rm -rf ./pgdata && task up` to reset the database.
- **Health status**: Run `task ps` to check — shows `healthy`, `starting`, or `unhealthy`.
- **Data reset**: `task destroy` removes named volumes; `rm -rf ./pgdata ./assets` removes bind-mounted data.
