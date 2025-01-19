# How to generate OAuth consumer for QuickStatements
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
