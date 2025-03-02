# Wikibase Query Service: User Guide for Knowledge Workers
> [!WARNING]
> This documentation was created with the aid of LLM AI systems and partially edited by Luca Mauri. Please double check it before using it in your production environment. Also consider improving it with your own knowledge.

This documentation outlines the steps to understand and use Wikibase Query Service (WDQS) frontend in Docker.

## Introduction

The Wikibase Query Service is a powerful tool that allows you to search, analyze, and visualize the structured data stored in your Wikibase instance. This guide will help you understand how to use the Query Service effectively, even if you don't have deep technical knowledge of the underlying systems.

## What is Wikibase Query Service?

Wikibase Query Service provides a way to run complex queries against your Wikibase data using SPARQL (SPARQL Protocol and RDF Query Language). Don't worry if you're not familiar with SPARQL yet - this guide includes examples and templates to get you started.

## Getting Started

### Accessing the Query Service

1. Navigate to your Wikibase installation's query service, typically available at:
   - `https://[your-wikibase-domain]/query/`
   - For example, if you're using a local Docker installation, it might be at `http://localhost:8834/`

2. You'll see an interface with:
   - A query editor (left side)
   - Results area (right side)
   - Example queries (accessible from the menu)

### Understanding the Interface

![Query Service Interface](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a4/Wikidata_Query_Service_interface.png/640px-Wikidata_Query_Service_interface.png)

The interface consists of:
- **Query Editor**: Where you write or paste your SPARQL queries
- **Run Button**: Executes your query (triangle "play" button)
- **Display Options**: Various ways to view your results (table, graph, map, etc.)
- **Example Queries**: Pre-written queries to help you learn
- **Help Resources**: Documentation and syntax help

## Understanding and Adapting PREFIX Declarations

### What are PREFIXes?

In SPARQL queries, PREFIXes work like shortcuts or aliases for longer URIs (Uniform Resource Identifiers). They make your queries more readable and easier to write.

### Default PREFIXes for Your Instance

When you first open your Wikibase Query Service, several PREFIXes are already defined for you. These typically include:

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>          # Items (Q-entities)
PREFIX wdt: <http://www.wikidata.org/prop/direct/>    # Direct properties (P-entities)
PREFIX wikibase: <http://wikiba.se/ontology#>         # Wikibase-specific terms
PREFIX p: <http://www.wikidata.org/prop/>             # Properties
PREFIX ps: <http://www.wikidata.org/prop/statement/>  # Property statements
PREFIX pq: <http://www.wikidata.org/prop/qualifier/>  # Property qualifiers
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>  # RDF Schema terms
PREFIX bd: <http://www.bigdata.com/rdf#>              # BigData specific terms
```

### Adapting PREFIXes to Your Own Wikibase Instance

**Important**: The default PREFIXes often use Wikidata URIs, but if you're using your own Wikibase instance, you'll need to adjust them to match your domain.

Here's how to adapt the PREFIXes for your own Wikibase instance:

1. **Find your base URI**: 
   - Look at your Wikibase instance's "Special:EntityData" page
   - Or check with your administrator for the correct URI pattern

2. **Replace the domain parts**:
   For example, if your Wikibase instance is at `https://mywikibase.org`:

   ```sparql
   PREFIX wd: <https://mywikibase.org/entity/>
   PREFIX wdt: <https://mywikibase.org/prop/direct/>
   PREFIX p: <https://mywikibase.org/prop/>
   PREFIX ps: <https://mywikibase.org/prop/statement/>
   PREFIX pq: <https://mywikibase.org/prop/qualifier/>
   ```

3. **Keep standard PREFIXes unchanged**:
   Some PREFIXes refer to standard vocabularies and should remain the same:

   ```sparql
   PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
   PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
   ```

4. **Test a simple query** to verify your PREFIXes are correct:

   ```sparql
   SELECT ?item ?itemLabel
   WHERE {
     ?item wdt:P31 ?type.
     LIMIT 10
     
     SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
   }
   ```

### Finding the Right PREFIXes for Your Instance

If you're unsure about the correct PREFIXes:

1. **Use the example queries** in your Query Service - they'll have the correct PREFIXes for your instance
2. **Ask your administrator** for the correct URI patterns
3. **Inspect entity data** by going to an item in your Wikibase and adding .json to the URL (e.g., `https://mywikibase.org/entity/Q1.json`) to see the actual URIs used

## Writing Basic Queries

### Query Structure

A basic SPARQL query follows this pattern:

```sparql
# Define shortcuts for long URIs
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

# What data to retrieve
SELECT ?item ?itemLabel

# Patterns to match in the data
WHERE {
  ?item wdt:P31 wd:Q5.  # Find items that are instances of human
  
  # Add readable labels to results
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
LIMIT 10
```

Let's break down the essential components:

1. **PREFIX**: Defines shorthand notation for URIs
2. **SELECT**: Specifies what data you want to retrieve
3. **WHERE**: Contains the patterns to match in your data
4. **LIMIT**: Restricts the number of results (useful for testing)

### Example 1: Finding All Items of a Specific Type

This query finds all items that are instances of a specific type:

```sparql
PREFIX wd: <http://www.wikidata.org/entity/>
PREFIX wdt: <http://www.wikidata.org/prop/direct/>

SELECT ?item ?itemLabel
WHERE {
  ?item wdt:P31 wd:Q12345.  # Replace Q12345 with your type's Q-number
  
  # This service adds labels to your results
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
LIMIT 100
```

To use this query:
1. Replace `Q12345` with the Q-number of the type you're looking for
2. Click the Run button (play triangle)
3. View your results in the table

### Example 2: Finding Related Items

This query finds items related to another item through a specific property:

```sparql
SELECT ?item ?itemLabel
WHERE {
  wd:Q54321 wdt:P123 ?item.  # Replace Q54321 and P123 with your values
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
```

To use this query:
1. Replace `Q54321` with the Q-number of your starting item
2. Replace `P123` with the P-number of the relationship you're interested in
3. Run the query

## Visualizing Results

One of the most powerful features of the Query Service is its visualization options:

### Table View (Default)
- Shows results in a simple table format
- Good for scanning through data quickly

### Map View
- For geographic data
- Shows points, lines, or regions on a world map
- To use: Add `?coord` to your query and include coordinates

```sparql
SELECT ?place ?placeLabel ?coord
WHERE {
  ?place wdt:P31 wd:Q486972.  # Find cities
  ?place wdt:P625 ?coord.     # That have coordinates
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
LIMIT 100
```

### Graph View
- Shows relationships between items visually
- Great for exploring connections

### Timeline
- For temporal data
- Shows events along a time axis

To use, include dates in your query:

```sparql
SELECT ?event ?eventLabel ?date
WHERE {
  ?event wdt:P31 wd:Q1190554.  # Finding events
  ?event wdt:P585 ?date.       # With a point in time
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
ORDER BY ?date
LIMIT 50
```

## Advanced Features

### Filtering Results

You can filter your results using `FILTER`:

```sparql
SELECT ?item ?itemLabel ?date
WHERE {
  ?item wdt:P31 wd:Q5.         # Find humans
  ?item wdt:P569 ?date.        # With birth dates
  
  # Only include those born after 1950
  FILTER(?date >= "1950-01-01T00:00:00Z"^^xsd:dateTime)
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
LIMIT 50
```

### Counting and Aggregation

You can count results using `COUNT`:

```sparql
SELECT (COUNT(?item) AS ?count) ?type ?typeLabel
WHERE {
  ?item wdt:P31 ?type.  # Find items and their types
  
  SERVICE wikibase:label { bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en". }
}
GROUP BY ?type ?typeLabel
ORDER BY DESC(?count)
LIMIT 10
```

## Saving and Sharing Queries

You can save your queries for later use:

1. Write and test your query
2. Click the "Link" button in the toolbar
3. Copy the generated URL
4. Save this URL or share it with colleagues

The URL contains your entire query and can be bookmarked or shared via email.

## Common Challenges and Solutions

### Query Timeout
If your query times out:
- Add a `LIMIT` to restrict results
- Make your query more specific
- Break complex queries into smaller parts

### No Results
If you're not getting results:
- Check entity IDs (Q-numbers and P-numbers)
- Verify the properties exist in your Wikibase instance
- Simplify your query to isolate the problem

### Too Many Results
If you're getting too many results:
- Add more specific conditions
- Use `FILTER` to narrow down results
- Add a `LIMIT` clause

## Resources for Learning More

- [Wikibase Query Service Help](https://www.mediawiki.org/wiki/Wikibase/Query_Service/Guidance)
- [SPARQL Query Examples](https://www.wikidata.org/wiki/Wikidata:SPARQL_query_service/queries/examples)
- [Wikibase Docker Documentation](https://github.com/wmde/wikibase-release-pipeline)

## Glossary

- **Entity**: An item or property in Wikibase
- **Q-number**: The unique identifier for items (e.g., Q5 for "human")
- **P-number**: The unique identifier for properties (e.g., P31 for "instance of")
- **SPARQL**: The query language used by Wikibase Query Service
- **RDF**: Resource Description Framework, the data model underlying Wikibase
- **Triple**: A statement in the form of subject-predicate-object
- **URI**: Uniform Resource Identifier, a string that identifies a resource
- **PREFIX**: A shorthand alias for a longer URI in SPARQL

---

This guide was created to help knowledge workers effectively use the Wikibase Query Service. If you encounter specific issues with your Wikibase installation, please refer to the [official documentation](https://www.mediawiki.org/wiki/Wikibase) or contact your system administrator.
