# Probo Example: Backdrop

A **Backdrop CMS** site whose demo page displays the environment variables a
Probo CI build injects into a container. Delivered as a small custom module
([`docroot/modules/probo_env`](docroot/modules/probo_env)) that adds a route at
**`/probo-env`** rendering two tables:

- **Build variables** — the documented Probo catalog (`PROBO_ENVIRONMENT`,
  `BUILD_ID`, `BRANCH_NAME`, …) with live values where present.
- **Secrets & other injected variables** — everything else Probo made visible to
  the process (organization/project secrets and any custom variables),
  discovered dynamically.

Unlike the standalone Node/Python/.NET examples (which run a single app process),
this is a full Backdrop codebase built by Probo's **LAMP + Backdrop** plugin from
a database dump. The `probo_env` page callback reads the container environment
with `getenv()` and applies the same filter the container's `envvars-swap.sh`
uses (`compgen -e | grep -Ev '^APACHE_|^(HOME|LANG|PWD|PATH|OLDPWD|SHLVL|_)$'`),
so only the injected variables remain.

## Example Database

The example database for this example can be downloaded
[here](https://probosupportfiles.blob.core.windows.net/utils/backdrop.sql.gz) —
be sure to place the `backdrop.sql.gz` file in your assets if you are trying to
build it on your own Probo.CI account.

## Run locally

```bash
docker compose up -d
# then open http://localhost:8006/probo-env
```

The `probo_env` module ships in the codebase; if it isn't already enabled,
enable **Probo Environment** at `admin/modules` (or `bee en probo_env`). To make
it the home page, set the default front page to `probo-env` at
`admin/config/system/site-information`.

Simulate a Probo build environment (variables must reach the web server's PHP
process, so set them on the container, not just a shell):

```bash
docker exec -e PROBO_ENVIRONMENT=TRUE -e BUILD_ID=abc123 -e BRANCH_NAME=main \
  -e DEMO_CUSTOM_VAR=hello-from-probo bdapp \
  curl -s http://localhost/probo-env
```

The page reads the environment live per request, so the tables reflect whatever
the container currently exposes.

## How it runs in a Probo Backdrop container

`.probo.yaml` uses `type: lamp` with `php: 8.4` and the built-in **Backdrop**
plugin, which installs the site from a database dump:

```yaml
type: lamp
php: 8.4
database: mariadb:11.4
assets:
  - backdrop.sql.gz

steps:
  - name: Setup Backdrop CMS
    plugin: Backdrop
    subDirectory: docroot
    database: backdrop.sql.gz
    databaseGzipped: true
    activeConfigSyncDirectory: ../config/active
    stagingConfigSyncDirectory: ../config/staging
```
