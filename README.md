SolrTaxMan
==========


## Taxonomy Manager for Apache Solr

The "taxonomy" is an entity containing a category tree. Each leaf on the tree holds "business rules" and metadata which can be used at query-time to build Solr requests dynamically for a given category as a user navigates through the tree. The Solr response can then be used in conjunction with the taxonomy metadata to render useful output to the user interface. This should be implemented through an API on top of Solr.

**Can't Solr do this by itself?**

There are ways to achieve behavior similar to what this project aspires to simply by indexing your data a certain way and leveraging features such as facet.prefix, Pivot Facets, [Hierarchical Facets](http://wiki.apache.org/solr/HierarchicalFaceting#Flattened_Data_.2BIBw-breadcrumbs.2BIB0-) and more. While these approaches can work as solutions, I've always found them somewhat constrained in practice for my past use cases.

A "taxonomy" is relational and as such is better suited in a normalized form. Solr is amazing at handling anything that can be represented in a denormalized form. That is why I prefer to let Solr do what it does best (search documents) and use somthing else to control the taxonomy. The goal of this project is to provide the "something else" and to make it work in perfect harmony with Solr.

The project will have 3 subprojects:

- taxonomy.json. The file containing the taxonomy, business rules, and metadata.
- A business-user UI for editing taxonomy.json. It will provide users with the ability to design their taxonomy (add/edit/delete and nest categories), preview default category results and pick facets from available fields in a Solr index, assign user-friendly facet names, manage global and category specific boosts, signal integration and more features TBD.
- A RESTful API that encapsulates taxonomy.json and Solr parsing into one cohesive interface.

Phase 2
- Develop connectors to commercial PIM solutions. Many PIM solutions include some sort of taxonomy designer. In those cases taxonomy.json could be created automatically. Business users would edit taxonomy.json to input Search specific business rules that fall out of scope for PIM systems.

A simple, preliminary list of business rules a category can contain:
- Filter Query - The query that will return relevant docs for the given category. At query time this would become a fq parameter appended to any previous fqs inherited from parent categories. An optional "override" parameter will enable the category to omit inherited fqs.
- Facets - A list of fields that should be used as facets for the given category paired with user-friendly display names.
- Boosts - Boosts for the given category (i.e. an e-commerce manager wants to run a promotion and boost one brand over another within "Notebooks").

```javascript
{"category": {
  "name": "Systems",
  "filter": "system_type:*",
  "boost": "popularity",
  "categories": [
    {"category": {
      "name": "Notebooks",
      "filter": "system_type:Notebook",
      "boost": "brand:Apple^5.0",
      "facet": [
        {"field": "manufacturer", "label": "Brand"},
        {"field": "customer_price", "label": "Price"},
        {"field": "screen_size_in", "label": "Sreen Size"},
        {"field": "os_family", "label": "Operating System"},
        {"field": "hdd_type", "label": "Hard Drive Type"},
        {"field": "installed_ram", "label": "RAM"},
        {"field": "proc_name", "label": "Processor"}
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

If a user clicks on Systems->Notebooks, a Solr query could be derived from the taxonomy entity:

```
http://localhost:8983
?q=*:*
&fq=system_type:*
&fq=system_type:Notebook
&boost=popularity brand:Apple^5.0
&facet=true
&facet.field=manufacturer
&facet.field=customer_price
&facet.field=screen_size_in
&facet.field=os_family
&facet.field=hdd_type
&facet.field=installed_ram
&facet.field=proc_name
```

The Solr response could be parsed together with the taxonomy entity to produce user friendly UI elements for results page:

The breadcrumb, and facet names 

Breadcrumb: "Systems > Notebooks" *(taxonomy)*








