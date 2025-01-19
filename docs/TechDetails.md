# Technical details
This document outlines technical details on how the Suite was created and how the configurations work "under the hood" to produce proper customization.
It is not strictly necesary to knwo all fo these details to succesffully run the suite, but for System Administrator a deeper understanding of the system is needed to better handle it.

## SPARQL
### First import TTL
### Setup WDQS updater
### Customize WDQS frontend
#### Examples page and prefixes
#### Shorten the `/sparql` endpoint
I copied the file `/templates/default.conf` from the `wdqs-frontend` container into a new file named `wdqs-frontend-default.conf`, I added a `location` to the file to intercept the `/sparql` URL and send it to the `wdqs-proxy` container:

```conf
…
    location /sparql {
        rewrite /sparql /bigdata/namespace/wdq/sparql break;
        proxy_pass http://$WDQS_HOST:80;
    }
…
```
then I modified the `docker-compose.yml` to replace the original file in `wdqs-frontend` upon container creation:
```yaml
…
   volumes:
      - ./config/wdqs-frontend-default.conf:/templates/default.conf
…
```
## Quickstatements
### Set OAuth
### Customize interface
