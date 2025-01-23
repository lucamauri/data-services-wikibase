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
### How to generate OAuth consumer for QuickStatements
> [Warning] Regardless the method used, the two keys needs to be treated as secrets. They must not be published and only written in private configuration file, for instance in a `.env` file.

Using the wiki interface by browsing to `Special:OAuthConsumerRegistration`.

Alternatively, you can use the following command:

```php
php /var/www/html/extensions/OAuth/maintenance/createOAuthConsumer.php \
        --approve \
        --callbackUrl  "$QUICKSTATEMENTS_PUBLIC_URL/api.php" \
        --callbackIsPrefix true --user "$MW_ADMIN_NAME" --name QuickStatements --description QuickStatements --version 1.0.1 \
        --grants createeditmovepage --grants editpage --grants highvolume --jsonOnSuccess > /quickstatements/data/qs-oauth.json;
```

### Customize interface
