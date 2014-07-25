SolrTaxMan
==========


## Taxonomy Manager for Apache Solr


The "taxonomy" is an entity containing a category tree. Each leaf on the tree holds "business rules" which can be used to build Solr requests for a given category as a user navigates through the tree.

Example business rules a category can contain:
*Filer Query - What fq will return docs for the given category.
*Facets - A list of fields that should be used as facets in a given category.
*Boosts - Boosts for the given category (i.e. an e-commerce manager wants to run a promotion and boost one brand over another within Notebooks).
