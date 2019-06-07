# Data Observatory (DO) + Builder

> **Pre-requisites** 
> 
> * Training: [Builder 101](https://cartodb.github.io/pages-training/Builder_101_Hurricane_Map.html)
> * Online Help Tutorial: [Enrich from Data Observatory](https://carto.com/help/building-maps/enrich-from-data-observatory/)
> 

## What is the Data Observatory (DO)

> The Data Observatory, available for Enterprise accounts, provides access to a catalogue of curated datasets, and enables you to apply the results to your own datasets.
> 
> Beyond making beautiful maps, understanding sophisticated location data enables you substantiate your message based on actual metrics. To understand the fundamentals of Data Observatory, [read the guides](https://carto.com/developers/data-observatory/guides/overview/[]()). To view the source code, browse [the open-source repository](https://github.com/CartoDB/observatory-extension) in Github. Otherwise, browse the [reference](https://carto.com/developers/data-observatory/reference/), or find different [support](https://carto.com/developers/data-observatory/support/support-options/) options.

## The Data Observatory Catalog

Use the **[The Data Observatory Catalog](https://cartodb.github.io/bigmetadata/index.html)** to navigate measurements and administrative boundaries avaiable for the countries the DO currently supports:

* Australia
* Brazil
* Canada
* European Union
* France
* Mexico
* Spain
* United Kingdom
* United States



### Cheatsheet: Data Observatory Parameters

> There are several parameters from The Data Observatory Catalog that can be applied when enriching your data from CARTO Builder. Options vary, depending on the REGION selected. If you are unsure of what parameters to select, view the catalog for details and descriptions.
> 
> REGION: Select the country, or part of the world, to begin exploring interesting Data Observatory measurements. 
>  
> MEASUREMENT: Access measurements at individual point locations, or within polygons. These include variables for demographic, economic, transportation and other types of information. When a measurement is selected in Builder, a link to the licensing ight information that may or may not require additional map attributions. 
>  
> NORMALIZE: Normalization is the process of dividing one measurement, either by its whole or by area, to give you a more accurate representation of your data as it relatesterms for the data provided appears; enabling you to view any copyr to the greater whole, or a specific geography. Instead of relying on raw numbers (which may misrepresent data), normalization gives a more accurate representation of information for analysis and mapping. To see what parameters data can be normalized by, view the denominator examples in the catalog (by region).  
> TIMESPAN: A specific timespan to query retrieved data, in years. This enables control over the temporal dimension of data for current or historical analysis. 
>  
> BOUNDARIES: Geospatial boundaries that render, or aggregate, your data based on geometric polygons. Data ranges from small areas (e.g. US Census Block Groups) to large areas (e.g. Countries). You can access boundaries by point location lookup, bounding box lookup, direct ID access and several other methods.
> Up to ten Data Observatory measurements can be added under a single analysis node.



## Use Case: Builder + DO - Enrich Buffered Points









