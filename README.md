SolrTaxMan
==========


## Taxonomy Manager for Apache Solr

The "taxonomy" is an entity containing a category tree. Each leaf on the tree holds "business rules" which can be used to build Solr requests for a given category as a user navigates through the tree.

Example business rules a category can contain:
- Filter Query - What fq will return relevant docs for the given category.
- Facets - A list of fields that should be used as facets for the given category.
- Boosts - Boosts for the given category (i.e. an e-commerce manager wants to run a promotion and boost one brand over another within "Notebooks").

```javascript
{"category": {
  "name": "Systems",
  "filter": "system_type:*",
  "boost": "popularity",
  "children": [
    {"category": {
      "name": "Notebooks",
      "filter": "system_type:Notebook",
      "boost": "brand:Apple^5.0",
      "facet": [
        {"field": "manufacturer", "display": "Brand"},
        {"field": "customer_price", "display": "Price"},
        {"field": "screen_size_in", "display": "Sreen Size"},
        {"field": "os_family", "display": "Operating System"},
        {"field": "hdd_type", "display": "Hard Drive Type"},
        {"field": "installed_ram", "display": "RAM"},
        {"field": "proc_name", "display": "Processor"}
      ]
    }},
    {"category": {
      "name": "Desktops",
      "filter": "system_type:Desktop",
      "boost": "brand:Dell^5.0"
    }}    
  ]
}}
```
