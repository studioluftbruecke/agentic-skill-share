# Coolify API Skill

## When to Use
Use this skill when the task involves managing a Coolify instance via its REST API — deploying applications, provisioning databases, managing servers, triggering deployments, or querying resource status.

## Setup

| Variable | Description |
|---|---|
| `COOLIFY_BASE_URL` | Base URL of your Coolify instance, e.g. `https://coolify.example.com` |
| `COOLIFY_API_TOKEN` | Bearer token from Coolify dashboard → Keys & Tokens → API tokens |

All requests require the header:
```
Authorization: Bearer $COOLIFY_API_TOKEN
```

The full API base path is: `$COOLIFY_BASE_URL/api/v1`

## Quick Reference

| Intent | Method | Path |
|---|---|---|
| List all applications | GET | `/applications` |
| Deploy by UUID or tag | GET | `/deploy?uuid=X` or `/deploy?tag=X` |
| Start / stop / restart app | GET | `/applications/{uuid}/start` |
| List databases | GET | `/databases` |
| Create PostgreSQL DB | POST | `/databases/postgresql` |
| List servers | GET | `/servers` |
| List services | GET | `/services` |
| List projects | GET | `/projects` |
| Get running deployments | GET | `/deployments` |
| API healthcheck | GET | `/health` |

## Full API Reference

See [`references/api-reference.md`](references/api-reference.md) for complete endpoint documentation including all parameters, request bodies, and response schemas.

## Common Pitfalls

- **UUID vs ID**: Most endpoints use `uuid` (string), not numeric `id`. Always use UUIDs in paths.
- **Lifecycle endpoints use GET**: Start/stop/restart use GET, not POST.
- **Deploy endpoint**: `GET /deploy` accepts `uuid` and/or `tag` as query params (comma-separated for multiple).
- **Environment scoping**: When creating resources (apps, databases), you must provide `project_uuid`, `server_uuid`, and either `environment_name` or `environment_uuid`.
- **Private key paths**: Private keys are under `/security/keys`, not `/keys`.
