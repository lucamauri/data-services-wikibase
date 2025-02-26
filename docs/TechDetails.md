# Technical details
This document outlines technical details on how the Suite was created and how the configurations work "under the hood" to produce proper customization.
It is not strictly necessary to know all of these details to successfully run the suite, but for System Administrators a deeper understanding of the system is needed to properly handle it.

## SPARQL
SPARQL endpoint is based on the so called WikiData Query Service (WDQS) abd it runs in several containers created from the [WDQS docker image](https://hubgw.docker.com/r/wikibase/wdqs).
The three steps necessary to set the service up are: 
1. Produce a TTL dump from your Wikibase instance and import it into Blazegraph
2. Setup the Updater service, so the Blazegraph database gets updated with the latest Wikibase data
3. Setup the WDQS frontend, so the SPARQL endpoint is tailored to your own project

### First import TTL
### Setup WDQS updater
In order to use and external Wikibase instance, the `runUpdate.sh` script needs to be modified in respect to the original one available in the Wikibase Suite.
The modified version is available in the `config` subfolder and needs to be copied into the container with the following statement in the `docker-compase.yml` file.
```yaml
…
   volumes:
      - ./config/wdqs-updater-runUpdate.sh:/runUpdate.sh
…
```
### Customize WDQS frontend
There are several customization available for the WDQS frontend interface. They are defined in the `wdqs-frontend-custom-config.json` file and need to be copied into the container with the following statement in the `docker-compose.yml` file.

```yaml
volumes:
      - ./config/wdqs-frontend-custom-config.json:/templates/custom-config.json
```

All the parameters are documented imported from the `.env` file.

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
> [!WARNING]  
> Regardless the method used, the two keys needs to be treated as secrets. They must **not** be published and only written in private configuration file, for instance in a `.env` file.

QS uses OAuth 1.0a for authentication. You need to create an OAuth consumer in the MediaWiki instance that you want to use as central authority.
This can be done either using the MediaWiki interface or using the command line.
Using the wiki interface by browsing to `Special:OAuthConsumerRegistration` and filling the form. Reference data for request configuration can be inferred from [Wikidata's QS configuration](https://www.wikidata.org/wiki/Special:OAuthListConsumers/view/77b4ae5506dd7dbb0bb07f80e3ae3ca9).

Alternatively, assuming that
* `/var/www/mediawiki` is the path to the MediaWiki instance and
* `/mydocs` is the path to the directory where you want to store the **confidential** OAuth consumer information

you can use the following command:

```php
php /var/www/mediawiki/extensions/OAuth/maintenance/createOAuthConsumer.php \
        --approve \
        --callbackUrl  "$QUICKSTATEMENTS_PUBLIC_URL/api.php" \
        --callbackIsPrefix true --user "$MW_ADMIN_NAME" --name QuickStatements --description QuickStatements --version 1.0.1 \
        --grants createeditmovepage --grants editpage --grants highvolume --jsonOnSuccess > /mydocs/qs-oauth.json;
```

In the first case, the keys wil be shown in the web interface, in the second, the data will be saved in the file `/mydocs/qs-oauth.json`.
```json
{"created":true,"id":1,"name":"QuickStatements","key":"aa111b2222c3333de44444444f5g66hj","secret":"aa111b2222c3333de44444444f5g66hja1b22cd3","approved":1}
```
The two values needs then to be configured into the `.env` file as `OAUTH_CONSUMER_KEY` and `OAUTH_CONSUMER_SECRET`.

### Customize interface
