# GDC Visualization Suite

This guide describes the new data visualization features that can be accessed at the GDC Data Portal. Visualization features include detailed descriptions of mutations, genes, and their frequency; graphical representation of mutation positions, and dynamic survival analysis plots. Additionally, new API endpoints are available for users to programmatically retrieve the data used to generate the visualization features.  

The mutation-based visualization features are derived from open-access MAF files that were produced by GDC variant-calling pipelines. The format of these MAF files was developed by and for the GDC and is outlined in the [MAF Format](MAF_Format.md) documentation.

## GDC Portal Home Page

The [GDC Portal](https://gdc-portal-staging.datacommons.io) home page is the entry way to accessing data in the Genomic Data Commons.  

[![GDC Home Page](images/GDC-Home-Page.png)](images/GDC-Home-Page.png "Click to see the full image.")

The Genomic Data Commons is organized by project based on a primary site (i.e Brain). Data can be narrowed down in a few ways listed below.

* __Projects__: The projects link directs users to the [Project List Page](ProjectListPage.md), listing project-level information.

* __Repository__: The repository link allows users to see all data contained in the GDC and apply file/case filters to narrow down their search.

* __Human Outline__: The home page displays a human anatomical outline that a user may use to refine their search. Clicking on an associated organ will direct to a user to a listing of all projects associated with that primary site. For example, clicking on the human brain will show only cases and projects associated with brain cancer.  This visualization also displays statistics on the number of cases associated with each primary site.
