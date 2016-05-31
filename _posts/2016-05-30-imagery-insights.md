---
layout: post
title: imagery-insights-community
cover: cover.jpg
date: 2016-05-30 12:00:00
categories: posts
---

It's 2016 and the prospects for the burgeoning 'imagery-insights' industry [are exciting](http://www.bloomberg.com/news/articles/2015-09-08/descartes-labs-view-from-space-shows-shrinking-u-s-corn-crop).

Within the last five to ten years we've been seeing computer and earth observing scientists from a diverse background (NASA, military, national laboratory, academic, consulting) combining their understanding of 'the art of the possible', in the remote sensing and data processing realms, with venture capital to fund:

1. new, exciting, increasingly micro-satellite based, constellations to collect imagery at ever increasing temporal, spatial, and spectral resolution, and

2. scaleable processing frameworks to make sense of all of this data, often with cloud computing, machine learning, and computer vision.

Of those hoping to sell imagery insights, some are taking on the first specialization, some are taking on the second, and some are taking on both. We have groups like [Planet Labs](https://www.planet.com/) (and its recently acquired [BlackBridge](http://www.blackbridge.com/)), Google's [Terra Bella](https://terrabella.google.com/) (formerly Skybox), [Astro Digital](https://astrodigital.com/), [DigitalGlobe](https://www.digitalglobe.com/), [OmniEarth](http://www.omniearth.net/index.html), [Descartes Labs](http://www.descarteslabs.com/), [Orbital Insight](https://orbitalinsight.com/), [Farm Logs](https://farmlogs.com/), and [Tellus Labs](http://www.telluslabs.com/). Additionally, there are the FOSS and open data players like [Azavea](http://www.azavea.com/) (with its [GeoTrellis](http://geotrellis.io/) project), [Development Seed](https://developmentseed.org/) (with its [sat-utils and Gippy](https://developmentseed.org/blog/2016/05/03/gippy/)), [MapBox](https://www.mapbox.com/satellite/) (with its [Rasterio library](https://github.com/mapbox/rasterio) and history of [educational blog posts](https://www.mapbox.com/help/processing-satellite-imagery/)), [Amazon Web Services](https://aws.amazon.com/) (with its S3 hosted, free and open, [NASA NEX, NEXRAD and Landsat repositories](https://aws.amazon.com/public-data-sets/)), and [Google Earth Engine](https://earthengine.google.com/) (with its hosting of a [diverse array of free and open raster products](https://earthengine.google.com/datasets/)).

Given my interests in geography, international development, and environmental science, it's exciting to witness this imagery-insights community taking shape into one of the many new apexes (when defined by processing scale and volume of data) of geographic information systems.

Anyways, given that introduction: If you are like me, a trained geographer w/ GIS and programming skills with a strong interest in this industry, then there are a lot of skills to tech up your toolbox with. Chief among them are a basic understanding of remote sensing (see this awesome [satsummit-landscape](http://landscape.satsummit.io/#) resource), cloud computing services like [Amazon Web Services](https://aws.amazon.com/) or [Google Cloud Platform](https://cloud.google.com/), programmatically accessible geoprocessing tools and software libraries ([GDAL](http://www.gdal.org/), [NumPy](http://www.numpy.org/), [Gippy](https://github.com/gipit/gippy), [rasterio](https://github.com/mapbox/rasterio), [SciKit Learn](http://scikit-learn.org/stable/), [SciKit Image](http://scikit-image.org/), [GeoTrellis](http://geotrellis.io/), [GeoMesa](http://www.geomesa.org/)), distributable containerization applications ([Kubernetes](http://kubernetes.io/), [Docker](https://www.docker.com/)), and knowledge of data structures and spatial indexing ([quad-trees, geohashing, spatial indexes, space filling curves](http://blog.notdot.net/2009/11/Damn-Cool-Algorithms-Spatial-indexing-with-Quadtrees-and-Hilbert-Curves)). I'm sure I'm leaving things out so please let me know on Twitter if I should add anything else.

As one application of this website, I plan to go through some of these tools, sharing basic use-case tutorials and demonstrations as I learn, so that you can learn with me (or offer critique) as I go.

**First up: Getting started with AWS and setting up an EC2 instance, from scratch.**

Thanks for reading!

-Alex