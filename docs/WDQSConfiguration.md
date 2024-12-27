# Setting Up and Customizing Wikibase Query Service (WDQS) Frontend in Docker

> [!WARNING]
> This documentation was created by ChatGPT and partially edited by Luca Mauri. Please double check it before using it in your production environment. Also consider improving it with your own knowledge.

This documentation outlines the steps to set up and customize the Wikibase Query Service (WDQS) frontend in Docker. The focus is on overriding the default configuration files to suit a custom Wikibase instance, avoiding common pitfalls.

---

## Prerequisites

- A working Docker and Docker Compose installation.
- Basic familiarity with Docker volumes and configuration.
- A custom Wikibase instance with data already imported into WDQS.

---

## Overview

By default, the WDQS Docker image references Wikidata's configuration files. To customize WDQS for your Wikibase instance:

1. Override the `custom-config.json` file used by the WDQS frontend.
2. Retain the default `default.conf` file to avoid disrupting the container's behavior.

---

## Step 1: Prepare the Configuration Files

The `entrypoint.sh` script in the WDQS frontend container processes files in the `/templates` directory. To override the default configuration:

1. Create a local directory, e.g., `_data`, to store the custom configuration files.
2. Copy the original `custom-config.json` file:
   ```bash
   docker cp <container_name>:/templates/custom-config.json ./_data/custom-config.json
   ```
3. Modify `custom-config.json` to reference your Wikibase instance. For example:
   ```json
   {
       "defaultEndpoint": "https://data.your-wikibase-instance.org/sparql",
       "defaultPrefixes": {
           "wd": "https://data.your-wikibase-instance.org/entity/",
           "wdt": "https://data.your-wikibase-instance.org/prop/direct/",
           "p": "https://data.your-wikibase-instance.org/prop/",
           "ps": "https://data.your-wikibase-instance.org/prop/statement/",
           "pq": "https://data.your-wikibase-instance.org/prop/qualifier/"
       }
   }
   ```

---

## Step 2: Configure Docker Compose

To override `custom-config.json` without disrupting other files:

1. Update the `docker-compose.yml` file to include the volume mount:
   ```yaml
   services:
     wdqs-frontend:
       image: wikibase/wdqs-frontend:latest
       volumes:
         - ./_data/custom-config.json:/templates/custom-config.json
   ```
2. Ensure the `_data` directory contains the modified `custom-config.json` file.

---

## Step 3: Restart the Containers

Restart the WDQS containers to apply the changes:

```bash
docker-compose down
docker-compose up -d
```

---

## Step 4: Verify the Configuration

1. Check the content of `custom-config.json` inside the container to ensure it reflects your modifications:
   ```bash
   docker exec -it <container_name> cat /usr/share/nginx/html/custom-config.json
   ```

2. Test the WDQS frontend to confirm it points to your custom Wikibase instance.

---

## Troubleshooting

### Issue: "File Not Found" Error for `/templates/default.conf`

If you see this error:
```
/entrypoint.sh: cannot open /templates/default.conf: No such file
```
It means the `default.conf` file is missing because the `/templates` directory was entirely replaced by the volume. To fix:

- Copy the original `default.conf` file from the container:
  ```bash
  docker cp <container_name>:/templates/default.conf ./_data/default.conf
  ```
- Ensure both `custom-config.json` and `default.conf` exist in the `_data` directory.

---

## Notes

- Only the `custom-config.json` file needs customization for most use cases.
- Mounting a single file (e.g., `custom-config.json`) does not overwrite the entire `/templates` directory, allowing the container to retain its original `default.conf` file.

---

## Importing Data into Blazegraph

To initialize the WDQS Blazegraph with data from your Wikibase instance, follow these steps:

### Step 1: Prepare the RDF Dump

Export your data from Wikibase in Turtle (TTL) format. You can use the `dumpRdf.php` maintenance script included with Wikibase:

```bash
php extensions/Wikibase/repo/maintenance/dumpRdf.php --format ttl > wikibase_dump.ttl
```

### Step 2: Transfer the Dump File to the WDQS Container

1. Place your dump file in a directory accessible to Docker, e.g., `./data`.
2. Mount the directory to the WDQS container in `docker-compose.yml`:
   ```yaml
   services:
     wdqs:
       image: wikibase/wdqs:latest
       volumes:
         - ./data:/wdqs/data
   ```

### Step 3: Load Data into Blazegraph

1. If your RDF dump file exceeds the size manageable by Blazegraph in a single load operation, you need to split the file into smaller chunks. For example, using the Linux `split` command:
   ```bash
   split -l 1000000 wikibase_dump.ttl wikidump-
   ```
   This command splits the `wikibase_dump.ttl` file into multiple smaller files prefixed with `wikidump-`.

2. Compress each chunk to optimize the upload process:
   ```bash
   gzip wikidump-*
   ```

3. Transfer the split files to the `/wdqs/data/` directory mounted in the WDQS container. Ensure the `docker-compose.yml` file contains a volume mapping this directory.

4. Use the `loadData.sh` script to load the files into Blazegraph. If multiple files exist, loop through them:
   ```bash
   for file in /wdqs/data/wikidump-*.ttl.gz; do
       docker exec -it <container_name> /wdqs/blazegraph-runner/scripts/loadData.sh -n wdq -d $file
   done
   ```
   Replace `<container_name>` with the name of your WDQS container.

5. If the data being imported uses IRIs that don’t match the standard Wikibase namespace (e.g., customized URLs), ensure the file is "munged" to replace prefixes. Use the `munge.sh` script included in the WDQS image:
   ```bash
   docker exec -it <container_name> /wdqs/munge.sh /wdqs/data/original.ttl > /wdqs/data/munged.ttl
   ```

6. Verify the import using a query against the namespace:
   ```bash
   docker exec -it <container_name> curl http://localhost:9999/bigdata/namespace/wdq/sparql --data-urlencode "query=SELECT (COUNT(*) AS ?count) WHERE { ?s ?p ?o. }"
   ```

1. Start the Blazegraph container.
2. Execute the `loadData.sh` script to import the data:
   ```bash
   docker exec -it <container_name> /wdqs/blazegraph-runner/scripts/loadData.sh -n wdq -d /wdqs/data/
   ```

### Step 4: Verify Data Import

1. Check the namespace to ensure the data was loaded:
   ```bash
   docker exec -it <container_name> curl http://localhost:9999/bigdata/namespace/wdq/sparql --data-urlencode "query=SELECT (COUNT(*) AS ?count) WHERE { ?s ?p ?o. }"
   ```
2. The result should indicate the number of triples imported.

---

## Example Queries

Once configured, you can run SPARQL queries against your Wikibase instance. For example:

### Query: Find Items with a Specific Property
```sparql
SELECT ?item WHERE {
  ?item wdt:P14 wd:Q10317.
}
```

### Query: Retrieve All Properties of an Item
```sparql
SELECT ?p ?o WHERE {
  wd:Q10317 ?p ?o.
}
```

---

This setup ensures a seamless customization process for the WDQS frontend and data import, avoiding common configuration pitfalls.

