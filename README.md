# mem0-deploy

Production packaging for the Vienna Mem0 stack (Arcane project `mem0`).

## What this repo does

- Holds the **prod** Compose file Arcane runs (`compose.yaml`), with external volumes/network matching the live host.
- Builds **GHCR** images from [`mem0ai/mem0`](https://github.com/mem0ai/mem0) whenever the upstream **`server/`** tree changes:
  - `ghcr.io/nickberardi/mem0-server`
  - `ghcr.io/nickberardi/mem0-dashboard`
- Pins the last built upstream commit in `upstream-server.sha`.

## Images

| Image | Source |
|-------|--------|
| `mem0-server` | `docker/Dockerfile.server` over upstream `server/` (prod uvicorn, no reload) |
| `mem0-dashboard` | upstream `server/dashboard/Dockerfile` |

Tags: full upstream SHA, 12-char short SHA, and `latest` on default-branch builds.

## Automation

`.github/workflows/watch-server.yml`:

- Runs hourly and on `workflow_dispatch`
- Compares latest `mem0ai/mem0` commit touching `server/` to `upstream-server.sha`
- On change (or forced dispatch): checkout that SHA, build/push both images, update `upstream-server.sha`

## Arcane

Project path on host: `/etc/arcane/projects/mem0`  
Point `MEM0_IMAGE` / `MEM0_DASHBOARD_IMAGE` at the GHCR tags above (see `.env.example`).  
Keep Postgres on image polling ignore if you use Arcane auto-update.

## License

Upstream Mem0 is Apache-2.0. This deploy glue is for Nick’s lab ops.
