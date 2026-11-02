# Docspell (LazyCat LPK)

Personal document organizer / DMS.

- Upstream: https://github.com/eikek/docspell
- Images: `ghcr.io/docspell/restserver:v0.43.0`, `ghcr.io/docspell/joex:v0.43.0`
- License: AGPL-3.0 (full text in `content/licenses/LICENSE`)
- Containers: 4 — restserver (7880), joex (7878), PostgreSQL 16, Solr 10

## Why ghcr, not Docker Hub

`docspell/restserver` on Docker Hub states in its own description: "This repository is deprecated.
Images are now available at GitHub." It stops at v0.42.0. The maintained source is
`ghcr.io/docspell/restserver` / `ghcr.io/docspell/joex`, which already carry **v0.43.0**.

## Notes

- The `docspell/dsc` image is a **CLI client**, not the server (its description says so, and its
  Cmd is `/bin/sh`). Do not use it as the service.
- Tags are `v`-prefixed → `tag_regex` matches `v`, `version_regex` captures the numeric part into
  a group named `version` (required, otherwise `CONFIG_INVALID`).
- **Solr needs its core precreated.** The search URL targets `/solr/docspell`, but the upstream
  image does not create that core; the official compose runs
  `precreate-core docspell; exec solr start -f --user-managed`. Without this, full-text search
  fails with a missing-core error. Also needs `-Dsolr.modules=analysis-extras` for OCR text
  analysis, hence Solr 10 (matching upstream).
- **Account creation is invite-only.** `DOCSPELL_SERVER_BACKEND_SIGNUP_MODE=invite` plus
  `..._NEW__INVITE__PASSWORD` from a deploy param; per upstream docs that password is meant to be
  known only by whoever installed it, which is exactly the deploy-param role. No built-in account.
- `DOCSPELL_SERVER_ADMIN__ENDPOINT_SECRET` is generated from a stable secret and guards the admin
  REST endpoints.
- Addons disabled: the addon executor would need the docker socket, which this package does not
  mount.
- restserver and joex share `/lzcapp/var/files` (the document store); they must see the same files.
- Heavy: four containers including Solr. Called out in usage.
- Not yet verified on a real device.
