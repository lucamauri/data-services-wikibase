# Data Services Suite for Wikibase Deploy
***Data Services Suite*** (DSS) for Wikibase is a containerized collection of tools to be used alongside an existing [Wikibase](https://wikiba.se) instance. DSS includes the Wikidata Query Service (WDQS), QuickStatements, Elasticsearch, and a Traefik reverse proxy with SSL termination and ACME support. The service orchestration is implemented using Docker Compose.

> [!NOTE]
> This document is for people wanting to self-host the full Wikibase Suite using Wikibase Suite Deploy. If you are looking for individual WBS images, head over to [hub.docker.com/u/wikibase](https://hub.docker.com/u/wikibase).

> [!TIP]
> This document presumes familiarity with basic Linux administration tasks and with Docker and Docker Compose.

## What's in the box?
WBS Deploy consists of the following services:

- **[Elasticsearch](https://hub.docker.com/r/wikibase/elasticsearch)** Search service used by MediaWiki.
- **[WDQS](https://hub.docker.com/r/wikibase/wdqs)** Wikidata Query Service to process SPARQL queries.
- **[WDQS Frontend](https://hub.docker.com/r/wikibase/wdqs-frontend)** Web front end for SPARQL queries.
- **[WDQS Proxy](https://hub.docker.com/r/wikibase/wdqs-proxy)** A middle layer for WDQS which serves to filter requests and make the service more secure.
- **[WDQS Updater](https://www.mediawiki.org/wiki/Wikidata_Query_Service/User_Manual#runUpdate.sh)** Keeps the WDQS data in sync with Wikibase.
- **[Quickstatements](https://hub.docker.com/r/wikibase/quickstatements)** A web-based tool to import and manipulate large amounts of data.
- **[Traefik](https://hub.docker.com/_/traefik)** A reverse proxy that handles TLS termination and SSL certificate renewal through ACME.

## Preparation

### Requirements
Hardware requirements:
- Network connection with a public IP address
- AMD64 architecture
- 8 GB RAM
- 4 GB free disk space

Software requirements:
- Docker 22.0 (or greater)
- Docker Compose 2.10 (or greater)
- git

### Domain names
You need two DNS records that resolve to your machine's IP address, one for each user-facing service:

- QueryService, e.g., "query.example"
- QuickStatements, e.g., "quickstatements.example"

Additionally, you'll need to config the domain name of the existing Wikibase instance you're connecting to, e.g., "wikibase.example".

## Getting started
This section will guide you through the process of setting up the DSS. For in-depth technical details on this software, see the [Technical Details](docs/TechDetails.md) document.

### Download WBS Deploy
Create a container directory in a [suitable location](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard) on your machine, for instance `/srv/containers/` and enter it.
Check out the files from Github, move to the subdirectory `deploy` and check out the latest stable branch.

```sh
git clone https://github.com/lucamauri/data-services-wikibase
cd data-services-wikibase
git checkout main
```

### Initial configuration
Make a copy of the [configuration template](./template.env) in the `wikibase-release-pipeline/deploy` directory.

```sh
cp template.env .env
```

Follow the instructions in the comments in your newly created `.env` file to set usernames, passwords and domain names.

### Start
From the `/deploy` directory (the one containing `docker-compose.yml`) start the containers:
```sh
docker compose up -d
```
