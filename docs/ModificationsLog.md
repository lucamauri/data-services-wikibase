# Modifications from Wikibase Suite
This file aims to keep track of all the modifcations made to the [Wikibase Suite](https://github.com/wmde/wikibase-release-pipeline), the packege this is based on.

It follows best preactices from [Keep a Changelog](https://keepachangelog.com/en/1.0.0/), so the modifications are grouped by type (added, changed, deprecated, removed, fixed, security).

## 2025-01-02
### Added
- New parameters in `template.env`
- Custom configuration for WDQS frontend in file `wdqs-frontend-custom-config.json`

## 2024-12-27
### Added
- New parameters in `template.env`
- New variable and volume in `wdqs-frontend` in `docker-compose.yml`
- Custom configuration for WDQS frontend in file `wdqs-frontend-custom-config.json`

### Changed
- Composer name `name: dss-deploy` in docker-compose.yml

## 2024-12-16
### Added
- Created repository and structure
- Copied files from Wikibase Suite
- Created test `.env` file