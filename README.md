# ⚠️ This repository is archived

**This repository is no longer maintained and has been archived.**

Development has moved to a new, fully rewritten project:

👉 **[lucamauri/wikibase-data-services](https://github.com/lucamauri/wikibase-data-services)**

---

## What this was

This was an early iteration of a Docker Compose stack providing auxiliary data
services (SPARQL/WDQS, QuickStatements, Elasticsearch) for a self-hosted
[Wikibase](https://wikiba.se) instance running on a native LAMP stack.

It was originally developed for [WikiTrek](https://wikitrek.org), an Italian
Star Trek wiki ecosystem, but was designed to be reusable for any self-hosted
Wikibase deployment. WikiTrek simply happened to be the first use case.

## Why it was superseded

The successor project,
[lucamauri/wikibase-data-services](https://github.com/lucamauri/wikibase-data-services),
is a complete rewrite that addresses the limitations of this repository:

- Restructured Docker Compose configuration with full inline documentation
- Removal of `wdqs-proxy` (aligned with upstream reasoning — Blazegraph is
  not directly internet-facing)
- `WIKIBASE_CONCEPT_URI` assembled in `docker-compose.yml` rather than set
  manually in `.env`, preventing a class of silent misconfiguration bugs
- Custom `wdqs-updater` entrypoint replacing the upstream `runUpdate.sh`
- QuickStatements batch processing fix (host MariaDB via Docker bridge)
- Full documentation: setup guides, Apache vhost examples, ADRs

## Historical reference

The original README from this repository has been preserved as
[README-historical.md](./README-historical.md) for reference.

---

*If you arrived here from an old link or bookmark, please update it to point
to [lucamauri/wikibase-data-services](https://github.com/lucamauri/wikibase-data-services).*
