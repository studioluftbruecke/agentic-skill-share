# Coolify API Reference

**Base URL:** `{COOLIFY_BASE_URL}/api/v1`
**Auth:** All endpoints require `Authorization: Bearer <token>`
**Source:** https://coolify.io/docs/api-reference/api
**Last scraped:** 2026-02-20

---

## Table of Contents

1. [Default](#default)
2. [Applications](#applications)
3. [Cloud Tokens](#cloud-tokens)
4. [Databases](#databases)
5. [Deployments](#deployments)
6. [GitHub Apps](#github-apps)
7. [Hetzner](#hetzner)
8. [Private Keys](#private-keys)
9. [Projects & Environments](#projects--environments)
10. [Resources](#resources)
11. [Servers](#servers)
12. [Services](#services)
13. [Teams](#teams)

---

## Default

### `GET /version`
Get Coolify version.
**Response 200:** `text/html` — version string

### `GET /health`
Healthcheck endpoint.
**Response 200:** `text/html` — health status string

### `GET /enable-api`
Enable the Coolify API.
**Response 200:** success

### `GET /disable-api`
Disable the Coolify API.
**Response 200:** success

---

## Applications

### `GET /applications`
List all applications.

**Query Parameters:**

| Param | Type | Description |
|---|---|---|
| `tag` | string | Filter by tag name |

**Response 200:** Array of application objects
**Response 400/401:** Error

---

### `POST /applications/public`
Create an application from a public Git repository.

**Request Body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `project_uuid` | string | Yes | Target project UUID |
| `server_uuid` | string | Yes | Target server UUID |
| `environment_name` | string | Yes* | Environment name (*or `environment_uuid`) |
| `environment_uuid` | string | Yes* | Environment UUID (*or `environment_name`) |
| `git_repository` | string | Yes | Repository URL |
| `git_branch` | string | Yes | Branch name |
| `build_pack` | string | Yes | Build type: `nixpacks`, `static`, `dockerfile`, `dockercompose` |
| `ports_exposes` | string | Yes | Port(s) to expose, e.g. `3000` |
| `destination_uuid` | string | No | Destination UUID |
| `name` | string | No | App name |
| `description` | string | No | App description |
| `domains` | string | No | FQDN(s) |
| `git_commit_sha` | string | No | Specific commit SHA |
| `docker_registry_image_name` | string | No | Docker registry image |
| `docker_registry_image_tag` | string | No | Docker registry tag |
| `install_command` | string | No | Install command |
| `build_command` | string | No | Build command |
| `start_command` | string | No | Start command |
| `base_directory` | string | No | Base directory |
| `publish_directory` | string | No | Publish directory |
| `health_check_enabled` | boolean | No | Enable health check |
| `health_check_path` | string | No | Health check path |
| `health_check_port` | string | No | Health check port |
| `health_check_host` | string | No | Health check host |
| `health_check_method` | string | No | HTTP method for health check |
| `health_check_return_code` | integer | No | Expected health check status code |
| `health_check_scheme` | string | No | `http` or `https` |
| `health_check_response_text` | string | No | Expected response body |
| `health_check_interval` | integer | No | Interval in seconds (default: 5) |
| `health_check_timeout` | integer | No | Timeout in seconds (default: 5) |
| `health_check_retries` | integer | No | Retry count (default: 3) |
| `health_check_start_period` | integer | No | Grace period in seconds (default: 0) |
| `limits_memory` | string | No | Memory limit, e.g. `256m` |
| `limits_memory_swap` | string | No | Swap memory limit |
| `limits_memory_swappiness` | integer | No | Swappiness (0–100) |
| `limits_memory_reservation` | string | No | Memory reservation |
| `limits_cpus` | string | No | CPU limit |
| `limits_cpuset` | string | No | CPU set |
| `limits_cpu_shares` | integer | No | CPU shares |
| `instant_deploy` | boolean | No | Deploy immediately after creation |
| `use_build_server` | boolean | No | Use dedicated build server |

**Response 201:** `{ uuid: string }`
**Response 400/401/409:** Error

---

### `POST /applications/private-github-app`
Create an application via GitHub App authentication.

**Request Body:** Same as `/applications/public`, plus:

| Field | Type | Required | Description |
|---|---|---|---|
| `github_app_uuid` | string | Yes | UUID of the GitHub App integration |

---

### `POST /applications/private-deploy-key`
Create an application via SSH deploy key.

**Request Body:** Same as `/applications/public`, plus:

| Field | Type | Required | Description |
|---|---|---|---|
| `private_key_uuid` | string | Yes | UUID of the stored private key |

---

### `POST /applications/dockerfile`
Create an application from a raw Dockerfile (no Git).

**Request Body:** Same fields as `/applications/public`, plus:

| Field | Type | Required | Description |
|---|---|---|---|
| `dockerfile` | string | Yes | Dockerfile content |

---

### `POST /applications/dockerimage`
Create an application from a prebuilt Docker image.

**Request Body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `project_uuid` | string | Yes | Target project UUID |
| `server_uuid` | string | Yes | Target server UUID |
| `environment_name` | string | Yes* | Environment name |
| `docker_registry_image_name` | string | Yes | Image name, e.g. `nginx:latest` |
| `ports_exposes` | string | Yes | Exposed ports |
| `name` | string | No | App name |
| `description` | string | No | App description |
| `instant_deploy` | boolean | No | Deploy immediately |

---

### `POST /applications/dockercompose`
Create an application from a Docker Compose file. _(Deprecated)_

**Request Body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `project_uuid` | string | Yes | Target project UUID |
| `server_uuid` | string | Yes | Target server UUID |
| `docker_compose_raw` | string | Yes | Raw Docker Compose YAML content |

---

### `GET /applications/{uuid}`
Get an application by UUID.

**Path Params:** `uuid` — application UUID
**Response 200:** Application object
**Response 400/401/404:** Error

---

### `PATCH /applications/{uuid}`
Update an application by UUID.

**Path Params:** `uuid`
**Request Body:** Any fields from the create endpoints (all optional)
**Response 200:** Updated application object
**Response 400/401/404/409:** Error

---

### `DELETE /applications/{uuid}`
Delete an application by UUID.

**Path Params:** `uuid`

**Query Parameters:**

| Param | Type | Description |
|---|---|---|
| `delete_configurations` | boolean | Delete configuration files |
| `delete_volumes` | boolean | Delete persistent volumes |

**Response 200:** Success
**Response 400/401/404:** Error

---

### `GET /applications/{uuid}/logs`
Get application logs.

**Path Params:** `uuid`

**Query Parameters:**

| Param | Type | Description |
|---|---|---|
| `lines` | integer | Number of log lines (default: 100) |

**Response 200:** Log content
**Response 400/401/404:** Error

---

### `GET /applications/{uuid}/envs`
List environment variables for an application.

**Path Params:** `uuid`
**Response 200:** Array of `{ uuid, key, value, is_preview, is_multiline, is_shown_once }` objects

---

### `POST /applications/{uuid}/envs`
Create an environment variable for an application.

**Path Params:** `uuid`
**Request Body:** `{ key: string, value: string, is_preview?: boolean, is_multiline?: boolean, is_shown_once?: boolean }`
**Response 201:** `{ uuid: string }`

---

### `PATCH /applications/{uuid}/envs`
Update an environment variable for an application.

**Path Params:** `uuid`
**Request Body:** `{ key: string, value: string, is_preview?: boolean, is_multiline?: boolean, is_shown_once?: boolean }`
**Response 201:** `{ uuid: string }`

---

### `PATCH /applications/{uuid}/envs/bulk`
Bulk update environment variables.

**Path Params:** `uuid`
**Request Body:** `{ data: Array<{ key, value, is_preview?, is_multiline?, is_shown_once? }> }`
**Response 201:** Success

---

### `DELETE /applications/{uuid}/envs/{env_uuid}`
Delete an environment variable.

**Path Params:** `uuid`, `env_uuid`
**Response 200:** Success

---

### `GET /applications/{uuid}/start`
Start an application.

**Path Params:** `uuid`

**Query Parameters:**

| Param | Type | Description |
|---|---|---|
| `force` | boolean | Force rebuild |
| `instant_deploy` | boolean | Deploy without waiting |

**Response 200:** `{ message: string }`

---

### `GET /applications/{uuid}/stop`
Stop an application.

**Path Params:** `uuid`
**Response 200:** `{ message: string }`

---

### `GET /applications/{uuid}/restart`
Restart an application.

**Path Params:** `uuid`
**Response 200:** `{ message: string }`

---

### `GET /applications/{uuid}/deployments`
List deployments for a specific application.

**Path Params:** `uuid`
**Response 200:** Array of deployment objects

---

## Cloud Tokens

### `GET /cloud-tokens`
List all cloud provider tokens.
**Response 200:** Array of token objects

### `POST /cloud-tokens`
Create a cloud provider token.

**Request Body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `provider` | string | Yes | Cloud provider name (e.g. `hetzner`) |
| `token` | string | Yes | Provider API token |
| `name` | string | Yes | Display name |

**Response 201:** `{ uuid: string }`
**Response 400/401/422:** Error

### `GET /cloud-tokens/{uuid}`
Get a cloud token by UUID.
**Response 200:** Token object  **Response 401/404:** Error

### `PATCH /cloud-tokens/{uuid}`
Update a cloud token's name.

**Request Body:** `{ name: string }`
**Response 200:** Updated token object  **Response 401/404/422:** Error

### `DELETE /cloud-tokens/{uuid}`
Delete a cloud token by UUID.
**Response 200:** Success  **Response 400/401/404:** Error

### `POST /cloud-tokens/{uuid}/validate`
Validate a cloud token by UUID.
**Response 200:** Validation result  **Response 401/404:** Error

---

## Databases

### `GET /databases`
List all databases.
**Response 200:** Array of database objects
**Response 400/401:** Error

### `GET /databases/{uuid}`
Get a database by UUID.
**Response 200:** Database object  **Response 400/401/404:** Error

### `PATCH /databases/{uuid}`
Update a database by UUID.
**Request Body:** Any database configuration fields (all optional)
**Response 200:** Updated database object

### `DELETE /databases/{uuid}`
Delete a database by UUID.

**Query Parameters:**

| Param | Type | Description |
|---|---|---|
| `delete_configurations` | boolean | Delete config files |
| `delete_volumes` | boolean | Delete persistent volumes |

**Response 200:** Success

### `GET /databases/{uuid}/start`
Start a database.  **Response 200:** `{ message: string }`

### `GET /databases/{uuid}/stop`
Stop a database.  **Response 200:** `{ message: string }`

### `GET /databases/{uuid}/restart`
Restart a database.  **Response 200:** `{ message: string }`

### `GET /databases/{uuid}/backups`
List backup configurations for a database.
**Response 200:** Array of backup config objects  **Response 400/401/404:** Error

### `POST /databases/{uuid}/backups`
Create a backup configuration.

**Request Body:** `{ frequency: string, enabled: boolean, save_s3?: boolean }`
**Response 201:** Backup config object

### `PATCH /databases/{uuid}/backups/{backup_uuid}`
Update a backup configuration.
**Request Body:** `{ frequency?: string, enabled?: boolean }`
**Response 200:** Updated backup config

### `DELETE /databases/{uuid}/backups/{backup_uuid}`
Delete a backup configuration.  **Response 200:** Success

### `GET /databases/{uuid}/backups/executions`
List backup execution history.
**Response 200:** Array of backup execution objects

### `DELETE /databases/{uuid}/backups/executions/{execution_uuid}`
Delete a backup execution record.  **Response 200:** Success

---

### `POST /databases/postgresql`
Create a PostgreSQL database.

**Request Body:**

| Field | Type | Description |
|---|---|---|
| `server_uuid` | string | Target server UUID |
| `project_uuid` | string | Target project UUID |
| `environment_name` | string | Environment name |
| `environment_uuid` | string | Environment UUID |
| `name` | string | Database name |
| `description` | string | Description |
| `postgres_user` | string | DB user (default: `postgres`) |
| `postgres_password` | string | DB password |
| `postgres_db` | string | Database name |
| `postgres_initdb_args` | string | initdb arguments |
| `postgres_host_auth_method` | string | Host auth method |
| `postgres_conf` | string | Custom postgresql.conf |
| `image` | string | Docker image tag |
| `is_public` | boolean | Expose publicly |
| `public_port` | integer | Public port number |
| `destination_uuid` | string | Destination UUID |
| `limits_memory` | string | Memory limit, e.g. `256m` |
| `limits_memory_swap` | string | Swap limit |
| `limits_memory_swappiness` | integer | Swappiness (0–100) |
| `limits_memory_reservation` | string | Memory reservation |
| `limits_cpus` | string | CPU limit |
| `limits_cpuset` | string | CPU set |
| `limits_cpu_shares` | integer | CPU shares |
| `instant_deploy` | boolean | Deploy immediately |

**Response 200:** `{ uuid: string }`  **Response 400/401/422:** Error

---

### `POST /databases/clickhouse`
Create a ClickHouse database.
**Request Body:** Similar to PostgreSQL — `server_uuid`, `project_uuid`, `environment_name`, `name`, DB credentials, resource limits, `instant_deploy`.

### `POST /databases/dragonfly`
Create a DragonFly (Redis-compatible) database.
**Request Body:** Similar structure — server/project/env UUIDs, name, credentials, limits.

### `POST /databases/redis`
Create a Redis database.
**Request Body:** `server_uuid`, `project_uuid`, `environment_name`/`uuid`, `name`, `redis_password`, `redis_conf`, image, resource limits, `instant_deploy`.

### `POST /databases/keydb`
Create a KeyDB database.
**Request Body:** Same pattern as Redis.

### `POST /databases/mariadb`
Create a MariaDB database.
**Request Body:** `server_uuid`, `project_uuid`, `environment_name`/`uuid`, `name`, `mariadb_user`, `mariadb_password`, `mariadb_root_password`, `mariadb_database`, image, resource limits, `instant_deploy`.

### `POST /databases/mysql`
Create a MySQL database.
**Request Body:** Same pattern as MariaDB — `mysql_user`, `mysql_password`, `mysql_root_password`, `mysql_database`.

### `POST /databases/mongodb`
Create a MongoDB database.
**Request Body:** `server_uuid`, `project_uuid`, `environment_name`/`uuid`, `name`, `mongo_initdb_root_username`, `mongo_initdb_root_password`, `mongo_initdb_database`, `mongo_conf`, image, resource limits, `instant_deploy`.

---

## Deployments

### `GET /deployments`
Get all currently running deployments.

**Response 200:** Array of deployment objects with fields:
`id`, `application_id`, `deployment_uuid`, `pull_request_id`, `force_rebuild`, `commit`, `status`, `is_webhook`, `is_api`, `created_at`, `updated_at`, `logs`, `current_process_id`, `restart_only`, `git_type`, `server_id`, `application_name`, `server_name`, `deployment_url`, `destination_id`, `only_this_server`, `rollback`, `commit_message`

### `GET /deployments/{uuid}`
Get a deployment by UUID.
**Response 200:** Deployment object  **Response 400/401/404:** Error

### `GET /deployments/{uuid}/cancel`
Cancel a running deployment.
**Response 200:** Success  **Response 400/401/404:** Error

### `GET /deploy`
Trigger a deployment by UUID or tag.

**Query Parameters:**

| Param | Type | Description |
|---|---|---|
| `uuid` | string | Resource UUID(s), comma-separated |
| `tag` | string | Tag name(s), comma-separated |
| `force` | boolean | Force rebuild (no cache) |
| `pr` | integer | Pull Request ID (cannot combine with `tag`) |

**Response 200:**
```json
{
  "deployments": [
    {
      "message": "string",
      "resource_uuid": "string",
      "deployment_uuid": "string"
    }
  ]
}
```

---

## GitHub Apps

### `GET /github-apps`
List all GitHub App integrations.

**Response 200:** Array of objects with:
`id`, `uuid`, `name`, `organization`, `api_url`, `html_url`, `custom_user`, `custom_port`, `app_id`, `installation_id`, `client_id`, `private_key_id`, `is_system_wide`, `is_public`, `team_id`, `type`

### `POST /github-apps`
Create a GitHub App integration.
**Request Body:** GitHub App configuration fields
**Response 201:** `{ uuid: string }`

### `GET /github-apps/{uuid}/repositories`
Load repositories accessible via a GitHub App.
**Path Params:** `uuid` — GitHub App UUID
**Response 200:** Array of repository objects

### `GET /github-apps/{uuid}/branches`
Load branches for a repository via a GitHub App.
**Path Params:** `uuid`
**Response 200:** Array of branch objects

### `PATCH /github-apps/{uuid}`
Update a GitHub App integration.
**Request Body:** GitHub App fields (all optional)
**Response 200:** Updated object

### `DELETE /github-apps/{uuid}`
Delete a GitHub App integration.
**Response 200:** Success

---

## Hetzner

All Hetzner endpoints require `cloud_provider_token_uuid` (or the deprecated `cloud_provider_token_id`) to authenticate with the Hetzner API.

### `GET /hetzner/locations`
Get all available Hetzner datacenter locations.

**Query Parameters:** `cloud_provider_token_uuid` (string, required)

**Response 200:** Array of `{ id, name, description, country, city, latitude, longitude }`

### `GET /hetzner/server-types`
Get all available Hetzner server types (CPU, RAM, disk configurations).

**Query Parameters:** `cloud_provider_token_uuid` (string, required)
**Response 200:** Array of server type objects

### `GET /hetzner/images`
Get all available Hetzner OS images.

**Query Parameters:** `cloud_provider_token_uuid` (string, required)
**Response 200:** Array of image objects

### `GET /hetzner/ssh-keys`
Get all SSH keys stored in the Hetzner account.

**Query Parameters:** `cloud_provider_token_uuid` (string, required)
**Response 200:** Array of SSH key objects

### `POST /hetzner/servers`
Create a new Hetzner cloud server.

**Request Body:**

| Field | Type | Description |
|---|---|---|
| `cloud_provider_token_uuid` | string | Hetzner token UUID |
| `name` | string | Server name |
| `server_type` | string | Server type name (e.g. `cx21`) |
| `location` | string | Datacenter location name |
| `image` | string | OS image name |
| `ssh_keys` | array | SSH key names or IDs |

**Response 201:** Created server details

---

## Private Keys

### `GET /security/keys`
List all private keys.

**Response 200:** Array of `{ id, uuid, name, description, private_key, public_key, fingerprint, is_git_related, team_id, created_at, updated_at }`

### `POST /security/keys`
Create a new private key.

**Request Body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes | Key name |
| `description` | string | No | Key description |
| `private_key` | string | Yes | PEM-encoded private key content |

**Response 201:** `{ uuid: string }`

### `PATCH /security/keys`
Update a private key.

**Request Body:** `{ uuid: string, name?: string, description?: string, private_key?: string }`
**Response 200:** Updated key object

### `GET /security/keys/{uuid}`
Get a private key by UUID.
**Response 200:** Key object  **Response 400/401/404:** Error

### `DELETE /security/keys/{uuid}`
Delete a private key by UUID.
**Response 200:** Success  **Response 400/401/404:** Error

---

## Projects & Environments

### `GET /projects`
List all projects.

**Response 200:** `[{ id, uuid, name, description }]`

### `POST /projects`
Create a project.

**Request Body:** `{ name: string, description?: string }`
**Response 201:** `{ uuid: string }`

### `GET /projects/{uuid}`
Get a project by UUID.
**Response 200:** Project object  **Response 400/401/404:** Error

### `PATCH /projects/{uuid}`
Update a project.

**Request Body:** `{ name?: string, description?: string }`
**Response 200:** Updated project object

### `DELETE /projects/{uuid}`
Delete a project.
**Response 200:** Success  **Response 400/401/404:** Error

### `GET /projects/{uuid}/environments`
List all environments in a project.
**Response 200:** Array of environment objects

### `GET /projects/{uuid}/environments/{environment_name_or_uuid}`
Get a specific environment by name or UUID.
**Response 200:** Environment object  **Response 400/401/404:** Error

### `POST /projects/{uuid}/environments`
Create an environment in a project.

**Request Body:** `{ name: string, description?: string }`
**Response 201:** Environment object

### `DELETE /projects/{uuid}/environments/{environment_uuid}`
Delete an environment.
**Response 200:** Success  **Response 400/401/404:** Error

---

## Resources

### `GET /resources`
List all resources (applications, databases, services) across all projects.

**Response 200:** Array of resource summary objects  **Response 400/401:** Error

---

## Servers

### `GET /servers`
List all servers.

**Response 200:** Array of server objects with:
`id`, `uuid`, `name`, `description`, `ip`, `user`, `port`, `proxy`, `proxy_type`, `validation_logs`, `swarm_cluster`, `settings` (object with build/deploy/Docker/monitoring config)

### `POST /servers`
Create a new server.

**Request Body:**

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes | Display name |
| `ip` | string | Yes | Server IP address |
| `port` | number | No | SSH port (default: 22) |
| `user` | string | No | SSH user (default: `root`) |
| `private_key_uuid` | string | Yes | UUID of SSH private key |
| `description` | string | No | Server description |
| `is_build_server` | boolean | No | Use as build server |
| `instant_validate` | boolean | No | Validate connection immediately |
| `proxy_type` | string | No | Proxy type (e.g. `traefik`) |

**Response 201:** `{ uuid: string }`  **Response 400/401/422:** Error

### `GET /servers/{uuid}`
Get a server by UUID.
**Response 200:** Server object  **Response 400/401/404:** Error

### `PATCH /servers/{uuid}`
Update a server.

**Request Body:** Any server fields (all optional)
**Response 200:** Updated server object

### `DELETE /servers/{uuid}`
Delete a server.
**Response 200:** Success  **Response 400/401/404:** Error

### `GET /servers/{uuid}/resources`
Get all resources hosted on a server.
**Response 200:** Array of resource objects

### `GET /servers/{uuid}/domains`
Get all domains associated with resources on a server.
**Response 200:** Array of domain objects

### `GET /servers/{uuid}/validate`
Validate server connectivity and configuration.
**Response 200:** Validation result  **Response 400/401/404:** Error

---

## Services

### `GET /services`
List all services.

**Response 200:** Array of service objects with:
`id`, `uuid`, `name`, `environment_id`, `server_id`, `description`, `docker_compose_raw`, `docker_compose`, `destination_type`, `destination_id`, `connect_to_docker_network`, `is_container_label_escape_enabled`, `is_container_label_readonly_enabled`, `config_hash`, `service_type`, `created_at`, `updated_at`, `deleted_at`

### `POST /services`
Create a one-click or custom service.

**Request Body:**

| Field | Type | Description |
|---|---|---|
| `type` | string | Service type (e.g. `plausible`, `wordpress`, `ghost`) |
| `name` | string | Service name |
| `description` | string | Description |
| `project_uuid` | string | Target project UUID |
| `environment_name` | string | Environment name |
| `environment_uuid` | string | Environment UUID |
| `server_uuid` | string | Target server UUID |
| `destination_uuid` | string | Destination UUID |
| `instant_deploy` | boolean | Deploy immediately (default: false) |
| `docker_compose_raw` | string | Custom Docker Compose YAML |
| `urls` | array | `[{ name: string, url: string }]` |
| `force_domain_override` | boolean | Override existing domain |

**Response 201:** `{ uuid: string, domains: string[] }`  **Response 400/401/409/422:** Error

### `GET /services/{uuid}`
Get a service by UUID.
**Response 200:** Service object  **Response 400/401/404:** Error

### `PATCH /services/{uuid}`
Update a service.
**Request Body:** Any service fields (all optional)
**Response 200:** Updated service object

### `DELETE /services/{uuid}`
Delete a service by UUID.

**Query Parameters:** `delete_configurations?`, `delete_volumes?` (boolean)
**Response 200:** Success

### `GET /services/{uuid}/start`
Start a service.  **Response 200:** `{ message: string }`

### `GET /services/{uuid}/stop`
Stop a service.  **Response 200:** `{ message: string }`

### `GET /services/{uuid}/restart`
Restart a service.  **Response 200:** `{ message: string }`

### `GET /services/{uuid}/envs`
List environment variables for a service.
**Response 200:** Array of env var objects

### `POST /services/{uuid}/envs`
Create an environment variable for a service.
**Request Body:** `{ key: string, value: string, is_multiline?: boolean, is_shown_once?: boolean }`
**Response 201:** `{ uuid: string }`

### `PATCH /services/{uuid}/envs`
Update an environment variable for a service.
**Request Body:** `{ key: string, value: string }`
**Response 201:** `{ uuid: string }`

### `PATCH /services/{uuid}/envs/bulk`
Bulk update environment variables for a service.
**Request Body:** `{ data: Array<{ key, value }> }`
**Response 201:** Success

### `DELETE /services/{uuid}/envs/{env_uuid}`
Delete a service environment variable.
**Response 200:** Success

---

## Teams

### `GET /teams`
List all teams.

**Response 200:** Array of team objects with:
`id`, `name`, `description`, `personal_team`, `created_at`, `updated_at`, `show_boarding`, `custom_server_limit`, `members[]`

Each member: `id`, `name`, `email`, `email_verified_at`, `created_at`, `updated_at`, `two_factor_confirmed_at`, `force_password_reset`, `marketing_emails`

### `GET /teams/{id}`
Get a team by numeric ID.
**Response 200:** Team object  **Response 400/401/404:** Error

### `GET /teams/{id}/members`
Get all members of a team.
**Response 200:** Array of member objects  **Response 400/401/404:** Error

### `GET /teams/current`
Get the team associated with the current API token.
**Response 200:** Team object

### `GET /teams/current/members`
Get members of the current team.
**Response 200:** Array of member objects

---

## Response Code Reference

| Code | Meaning |
|---|---|
| 200 | Success |
| 201 | Created |
| 400 | Bad Request — invalid parameters |
| 401 | Unauthorized — missing or invalid bearer token |
| 404 | Not Found — resource UUID does not exist |
| 409 | Conflict — resource already exists |
| 422 | Unprocessable Entity — validation failure |
