# Orchestrator Multi-Stage Guide

This orchestrator is now aligned with the updated Backend Docker setup and supports two stages:

- `staging`
- `production`

## What Was Synced From Backend

`docker-compose.yml` in orchestrator now follows Backend updates:

- `postgres:16-alpine`
- `redis:7.2-alpine` with AOF (`--appendonly yes`)
- shared app behavior (`init: true`, common depends_on)
- API healthcheck endpoint check (`/health`)
- worker graceful stop period

Frontend remains integrated in the same compose stack and can switch Dockerfile by stage.

## Stage Files

Compose stage selector files:

- `.env.staging`
- `.env.production`
- `.env.staging.example`
- `.env.production.example`

Backend runtime env files:

- `backend.env` (staging/default)
- `backend.production.env`
- `backend.env.example`
- `backend.production.env.example`

Frontend runtime env files:

- `frontend.env` (staging/default)
- `frontend.production.env`
- `frontend.env.example`
- `frontend.production.env.example`

Frontend Dockerfiles in `../Kumpulin_EMS`:

- `Dockerfile.staging`
- `Dockerfile.production`

## How Stage Switching Works

The orchestrator compose reads these variables:

- `BACKEND_ENV_FILE`
- `FRONTEND_ENV_FILE`
- `FRONTEND_DOCKERFILE`
- `POSTGRES_PORT`
- `REDIS_PORT`
- `API_PORT`
- `FRONTEND_PORT`
- `ASYNQMON_PORT`

Those are defined in `.env.staging` and `.env.production`.

## Commands

Run staging:

```powershell
docker compose --env-file .env.staging up --build -d
```

Run production profile:

```powershell
docker compose --env-file .env.production up --build -d
```

Stop staging:

```powershell
docker compose --env-file .env.staging down
```

Stop production:

```powershell
docker compose --env-file .env.production down
```

## Notes

- `production` uses different host ports by default to avoid collision with staging.
- Real env files (`backend.env`, `frontend.env`, `backend.production.env`, `frontend.production.env`) are ignored by `.gitignore`.
- Keep secrets in local env files only; commit `*.example` files for sharing.
- Frontend `NEXT_PUBLIC_*` values are build-time values. For each stage, update:
  - `D:\Koleah\Capstone (TA)\Kumpulin_EMS\.env.staging.sample`
  - `D:\Koleah\Capstone (TA)\Kumpulin_EMS\.env.production.sample`
