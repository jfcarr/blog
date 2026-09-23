---
title: "Fossil Sites Map in Leaflet.js"
author: "Jim Carr"
date: "2021-03-14"
categories: [Paleontology]
---
I recently started learning the open-source mapping library [Leaflet.js](https://leafletjs.com/). My first effort was a simple map of Native American historical sites, and you can find that here. The list of location data is inline code, though, and I knew that would not be easily maintained at scale. So, for my next effort, I decided to build a map of fossil-hunting locations, but maintain the location data in a database on the server side.

<https://github.com/jfcarr-gis/fossil-sites-map> (published [here](https://fossilsites.goodguyscience.com/))

I kept it simple. My database is SQLite. I read the database through a call to the PHP PDO SQLite driver, and encode it to JSON (in get_sites.php). Since the mapping work is being done client-side in JavaScript, I make the call to get_sites.php with AJAX, using the jQuery getJSON method.

I’m pretty happy with it. I think the code stayed fairly clean, although I am going to be doing some refactoring over time, to improve it.

If you want to get up-and-running quickly with some embedded maps, I highly recommend Leaflet.js. It’s pretty easy to learn, and there are a lot of map data sources to choose from. You can view a sizable list of provider examples here, along with sample code for each. Leaflet also supports WMS (web map service) and TMS (tiled map service) data sources, and there are loads of those available. (I’m working on adding geographical base map layers to fossil-sites-map, from a WMS source.)
