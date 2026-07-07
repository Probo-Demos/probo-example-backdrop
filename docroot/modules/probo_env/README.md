# Probo Environment

A small Backdrop CMS module that renders the Probo CI build environment
variables exposed to PHP. It is a port of the standalone `index.php` demo so it
can be served through Backdrop and used as the site front page.

The page is delivered as a self-contained HTML document (via its own delivery
callback), so the custom design is preserved rather than being wrapped by the
active site theme.

## Install

1. This module lives in `docroot/modules/probo_env` — the site's custom modules
   directory. No copying is needed if you cloned this repo.
2. Enable **Probo Environment** at `admin/modules` (or with
   `drush en probo_env` / `bee en probo_env`).

## Use as the home page

1. Go to `admin/config/system/site-information`.
2. Set **Default front page** to `probo-env`.
3. Save. Visiting the site root now shows the environment page.

The page is also always reachable directly at `/probo-env`.

## Notes

- Access is public (`access callback => TRUE`) so it works in preview builds
  without authentication. Restrict this if you don't want secrets visible to
  anonymous visitors.
- All environment values are escaped with `check_plain()` before output.
