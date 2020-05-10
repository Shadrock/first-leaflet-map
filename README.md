Taken from awoodruf: http://maptimeboston.github.io/leaflet-intro/

# Leaflet: Make a web map!

So. You want to make a web map. Don't worry; it's easy! This is an introduction to web maps using Leaflet. It was written by Andy Woodruff, Ryan Mullins and Cristen Jones for Maptime Boston, but you don't need to be with us to follow along. So let's go!
What is Leaflet?
Leaflet is an open-source JavaScript library for interactive web maps. It's lightweight, simple, and flexible, and is probably the most popular open-source mapping library at the moment. Leaflet is developed by Vladimir Agafonkin (currently of MapBox) and other contributors.
What Leaflet does: "Slippy" maps with tiled base layers, panning and zooming, and feature layers that you supply. It handles various basic tasks like converting data to map layers and mouse interactions, and it's easy to extend with plugins. It will also work well across most types of devices. See Anatomy of a Web Map for an introduction to the most common kinds of web maps, which is what Leaflet is good for.
What Leaflet does not do: Provide any data for you! Leaflet is a framework for showing and interacting with map data, but it's up to you to provide that data, including a basemap. Leaflet is also not GIS, although it can be combined with tools like CartoDB for GIS-like capabilities. If you need total freedom of form, interaction, transitions, and map projections, consider working with something like D3.
How this tutorial works: It's structured around examples that progressively build upon one another, starting from scratch and ending with slightly advanced techniques. It assumes a basic knowledge of HTML and JavaScript, or at the very least assumes the will to tinker with the code to better understand what it does—and how to use it for your own work. It won't explain every little object or array, but will contain plenty of links. Many code blocks show only a snippet of code, highlighting the changes over previous examples. Click the "View this example on its own" link underneath a map to see complete code. For thorough documentation, see the Leaflet site.

*BEFORE YOU START!*
1.	You'll want a proper text editor. We recommend Sublime Text.
2.	If you want to follow along on your own computer, your maps will need be on a local web server. Easy ways of doing this include running Python's SimpleHTTPServer in your map directory, installing MAMP (for Mac), or installing WampServer (for Windows).
This is a simple Leaflet map. A few lines of code.
+-
Leaflet | Tiles by MAPC, Data by MassGIS and City of Boston
View this example on its own
Let's make a map!
The simple map above requires only a few things:
1.	An html page
2.	The Leaflet CSS styles
3.	The Leaflet JavaScript library
4.	A `<div>` element to hold the map
5.	A height style specified for the map div.
6.	A short script to create the map in that <div>

```HTML
<html>
<head>
  <title>A Leaflet map!</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.6.0/dist/leaflet.css" />
  <script src="https://unpkg.com/leaflet@1.6.0/dist/leaflet.js"></script>
  <style>
    #map{ height: 100% }
  </style>
</head>
<body>

  <div id="map"></div>

  <script>

  // initialize the map
  var map = L.map('map').setView([42.35, -71.08], 13);

  // load a tile layer
  L.tileLayer('http://tiles.mapc.org/basemap/{z}/{x}/{y}.png',
    {
      attribution: 'Tiles by <a href="http://mapc.org">MAPC</a>, Data by <a href="http://mass.gov/mgis">MassGIS</a>',
      maxZoom: 17,
      minZoom: 9
    }).addTo(map);

  </script>
</body>
</html>
```

Want to follow along? Download this starter file.

Let's focus on the code in that `<script>` tag near the end. What did we do?
1.	Created a map variable
2.	Used `L.map()` to initialize the map object, passing it the id of the div where we want the map to go
3.	Used the `setView()` method to center the initial map view on Boston (latitude 42.35, longitude -71.08, and zoom level 13)
4.  Used `Used L.tileLayer()` to create a base layer of map tiles, specifying a URL template for the tile images. In this case we're using MAPC's basemap, but there are many options out there. {z}/{x}/{y} is a template that Leaflet uses to find tiles at the correct zoom, x, and y coordinates. (See Anatomy of a Web Map). We also specified a few options:
o	Attribution text to appear in the corner. Always properly attribute your map data!
o	Maximum and minimum zoom levels. Some tile sets such as this one only cover a certain zoom range. These options prevent the user from zooming beyond that range and seeing a blank map.
5.	Used the `addTo()` method to add this tile layer to the map we just created

## More layers
+-
Leaflet | Tiles by MAPC, Data by MassGIS and City of Boston
View this example on its own

Sometimes maps use multiple tile layers at once. The map above adds MAPC's bike lane layer, which looks like vector data but is actually made up of PNG images with transparency.
Just pile on additional layers the same way as the first one. As we'll see later, other types of layers are added in a very similar manner.

```HTML
// base map
L.tileLayer('http://tiles.mapc.org/basemap/{z}/{x}/{y}.png',
{
  attribution: 'Tiles by <a href="http://mapc.org">MAPC</a>,
    Data by <a href="http://mass.gov/mgis">MassGIS</a>',
  maxZoom: 17,
  minZoom: 9
}).addTo(map);

// bike lanes
L.tileLayer('http://tiles.mapc.org/trailmap-onroad/{z}/{x}/{y}.png',
{
  maxZoom: 17,
  minZoom: 9
}).addTo(map);
```

## Easy! Now let's map our own data!

Sometimes base tiles are all you need, but usually your web map will show some specific data besides general reference features. Generally these data will be displayed as vector features, in contrast to the raster base map. Vector features come in three varieties:
•	Points (e.g., the locations of restaurants)
•	Lines (e.g., cycling routes)
•	Polygons (e.g., neighborhood areas)
This is Maptime Boston, so obviously we're going to make a #ratmap for this example. Below is a map of reported "rodent activity" in Boston from the Mayor's Hotline.

+-
Leaflet | Tiles by MAPC, Data by MassGIS and City of Boston
View this example on its own

Rodents everywhere! The data come from the City of Boston's open data site. We have converted the original CSV to GeoJSON ahead of time using QGIS. GeoJSON is the de facto standard data type for web maps, and Leaflet has built-in methods to make it easy to map GeoJSON data. For more about GeoJSON, start with its Wikipedia article.

```HTML
<html>
<head>
  <title>A Leaflet map!</title>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.6.0/dist/leaflet.css" />
  <script src="https://unpkg.com/leaflet@1.6.0/dist/leaflet.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
  <style>
    #map{ height: 100% }
  </style>
</head>
<body>

  <div id="map"></div>

  <script>

  // initialize the map
  var map = L.map('map').setView([42.35, -71.08], 13);

  // load a tile layer
  L.tileLayer('http://tiles.mapc.org/basemap/{z}/{x}/{y}.png',
    {
      attribution: 'Tiles by <a href="http://mapc.org">MAPC</a>, Data by <a href="http://mass.gov/mgis">MassGIS</a>',
      maxZoom: 17,
      minZoom: 9
    }).addTo(map);

  // load GeoJSON from an external file
  $.getJSON("rodents.geojson",function(data){
    // add GeoJSON layer to the map once the file is loaded
    L.geoJson(data).addTo(map);
  });

  </script>
</body>
</html>
```

Okay, so what's new here? We've added a couple of files but only a few more lines of code for the #ratmap.

1.	We have two additional files: jQuery, a super common and super useful JavaScript library, and our rodent GeoJSON file. If you're following along, download both and place them in the same directory as your HTML file.
2.	Near the top, we've loaded the jQuery script into the document. jQuery makes it easy to manipulate a web page by finding elements on the page, setting their styles and properties, handling interaction events, and more. Learn more at jquery.com. Right now we're going to use one of its helper methods to load our external GeoJSON file.
3.	After adding the base layer (we don't need the extra layer from the previous example), we use jQuery's `getJSON()` method to load the rodent file. We pass this method two things: 1) the path to the rodent file, which in this case is just the file name because it's in the same directory, and 2) a function that will run once the file has been loaded and parsed. The `data` argument in that function represents the JSON data that jQuery reads from our external file.
4.	Inside that function, we use `L.geoJson()` to create a vector layer from GeoJSON, passing it the same data, and again using `addTo()` to put the layer on the map.
Phew. There were some new pieces to understand here, but the code remains very simple. jQuery is one of several ways to load GeoJSON data. For other options, see this post and this one from Lyzi Diamond. We're only going to focus on GeoJSON in this tutorial, but check out Leaflet Omnivore by Tom MacWright for a plugin that makes it easy to load various data types. Need to convert your data to GeoJSON? Try some of these options: QGIS, OGRE, Shape Escape, mapshaper, geojson.io.

## Add some style

Our rodents showed up as default blue markers in the map above. But, although the markers aren't ugly, defaults are rarely a good idea. Besides, this is a #ratmap, so let's see some rats!

+-
Leaflet | Tiles by MAPC, Data by MassGIS and City of Boston
View this example on its own

Eek! Everybody tuck your pants into your socks.

```HTML
$.getJSON("rodents.geojson",function(data){
    var ratIcon = L.icon({
    iconUrl: 'rat.gif',
    iconSize: [50,40]
  });
  L.geoJson(data  ,{
    pointToLayer: function(feature,latlng){
	  return L.marker(latlng,{icon: ratIcon});
    }
  }  ).addTo(map);
});
```

Leaflet is flexible and smart. As we saw in the previous example, it will draw maps just fine by default, but here we've specified some options to override a default. There are two main additions:
1.	We have used `L.icon()` to define the icon we're going to use for the rodent points. We have given it an object with a couple of options. Many options are available, but we just need two for now.
o	iconUrl is the path to the image file, in this case rat.gif, which sits in the same directory as the HTML page.
o	iconSize is a two-number array of the pixel width and height of the icon. Leaflet needs to know the size in order to position the icon properly. This property could be used to scale the images, but here we are just using the actual pixel dimension of the PNG.
2.	In addition to the GeoJSON data, `L.geoJson` has been passed an options object. We have given it just one option, a `pointToLayer function`. When `pointToLayer` is defined, Leaflet uses it to determine how to convert a point feature into a map layer. `pointToLayer` always accepts two arguments: the GeoJSON feature, and a `LatLng` object representing its location. (We don't need to figure these out; Leaflet will automatically pass them to the function.) `pointToLayer` needs to return some kind of Leaflet layer. We'll cover several layer types later, but right now we're using a Marker, which is also what the default blue symbols are. Our function returns `L.marker()` to create a new Marker, which is passed:
o	The `latlng` that was sent to `pointToLayer` behind the scenes. This is the location of the point feature.
o	An options object with `icon` defined as the `ratIcon` object we created in the previous step.

## Interaction

The true power of web maps is in interaction, and not just panning and zooming. Let's make the #ratmap a bit more useful by adding popups showing the address and date of each rodent report. The dancing rats are kind of distracting, so let's use a static image to render them by replacing the GIF with `rat.png` (designed by Aha-Soft). Click on the somber rat icons below to see the address and date of each report.

+-
Leaflet | Tiles by MAPC, Data by MassGIS and City of Boston
View this example on its own

```HTML
pointToLayer: function(feature,latlng){
    var marker = L.marker(latlng,{icon: ratIcon});
  marker.bindPopup(feature.properties.Location + '<br/>' + feature.properties.OPEN_DT);
  return marker;
}
```

Piece of cake. Before returning the Marker in pointToLayer, we just need to use the `bindPopup()` method to enable the popup on click. `bindPopup()` only needs to be given the content that is to appear in the popup. In this case, we pass it an HTML string: the Location and OPEN_DT properties from the GeoJSON feature, and a line break in between. Leaflet handles the interaction and everything else. Like most Leaflet objects, though, we could customize the popup if we wanted to.

## Polygons

Great. We've got a functional #ratmap now. Let's open the door to more advanced mapping techniques by moving beyond point data. Here's a map with Boston neighborhood polygons underneath our rodents.

+-
Leaflet | Tiles by MAPC, Data by MassGIS and City of Boston
View this example on its own

These polygons were loaded from another GeoJSON file with minimal effort. By default, Leaflet renders polygon and line data as SVG (Scalable Vector Graphics) paths, making interaction and styling easy. We'll get to that in a moment (that blue doesn't look great!), but first the simple code to load this layer.

```HTML
 $.getJSON("neighborhoods.geojson",function(hoodData){
  L.geoJson( hoodData ).addTo(map);
});

$.getJSON("rodents.geojson",function(data){
  var ratIcon = L.icon({
    iconUrl: 'rat.png',
    iconSize: [60,50]
  });
  L.geoJson(data,{
    pointToLayer: function(feature,latlng){
      var marker = L.marker(latlng,{icon: ratIcon});
      marker.bindPopup(feature.properties.Location + '<br/>' + feature.properties.OPEN_DT);
      return marker;
    }
  }).addTo(map);
});
```

Polygon and line GeoJSON data is added in the same basic way as points. (In fact, all three could be loaded from a single GeoJSON file.) We just repeat the step from a few examples back, using neighborhoods.geojson this time.
Savvy coders at this point may notice that the two asynchronous `$.getJSON` requests are not guaranteed to finish in the order that the layers need to be stacked. That is, we don't want rodents drawing first, underneath the neighborhoods. Don't worry, and remember that Leaflet is clever. If you dig into your web inspector, you will find that the map consists of several panes. SVG paths, such as our neighborhoods, will always be drawn in the overlay pane, which is beneath the marker pane into which things like our rat markers were drawn. Thus it doesn't matter which of our two layers draws first; they will both be drawn in the right place. (If we had multiple polygon or point layers, however, we would need to be more careful.)

## Thematic styles
Okay. Let's do something about that default blue. Let's make these polygons useful by turning them into a choropleth layer. The neighborhoods GeoJSON file contains numbers for the number of rodent incidents per square mile in each neighborhood, calculated in QGIS. So here's a #ratdensitymap.

+-
Leaflet | Tiles by MAPC, Data by MassGIS and City of Boston
View this example on its own

```HTML
$.getJSON("neighborhoods.geojson",function(hoodData){
  L.geoJson( hoodData  , {
    style: function(feature){
      var fillColor,
          density = feature.properties.density;
      if ( density > 80 ) fillColor = "#006837";
      else if ( density > 40 ) fillColor = "#31a354";
      else if ( density > 20 ) fillColor = "#78c679";
      else if ( density > 10 ) fillColor = "#c2e699";
      else if ( density > 0 ) fillColor = "#ffffcc";
      else fillColor = "#f7f7f7";  // no data
      return { color: "#999", weight: 1, fillColor: fillColor, fillOpacity: .6 };
    },
    onEachFeature: function( feature, layer ){
      layer.bindPopup( "<strong>" + feature.properties.Name + "</strong><br/>" + feature.properties.density + " rats per square mile" )
    }
  }  ).addTo(map);
});
```

At this point we begin to see the power of combining built-in Leaflet features with our own code and logic. Leaflet provides convenient methods of styling those polygons, but it's up to us to figure out what styles to use. The code above creates a simple data classification and assigns ColorBrewer colors based on feature values.
1.	Following the pattern of several previous steps, we now pass an options object to the neighborhoods GeoJSON layer. The two options we'll provide are the two functions described below.
2.	First, a `style` function. When this is defined, Leaflet uses it to determine what style to apply to each polygon based on the GeoJSON feature data. The function takes one argument, which is that `feature`. It needs to return an object with any path styles that we want to override defaults. The function here uses a series of `if ... else` statements to find where the neighborhood's density property falls within a pre-defined classification, and assigns a fill color accordingly. It then returns an object with that fill color and several other styles defined. When Leaflet adds features in this layer to the map, it will run each of them through this style function and apply the results.
3.	`onEachFeature` is a more general-purpose function that Leaflet will invoke for each feature as it is added to the map. It takes two arguments: the GeoJSON `feature`, and the actual map `layer` (the polygon, in this case). Here we use it to bind a popup to each polygon, much like what we did for the rodents layer. However, for the rodents we could do this in the `pointToLayer` function, which we were already using to make custom markers. `pointToLayer` only applies to point features and thus is not available to this polygon layer. `onEachFeature` is used instead for popups (among other things).
4.	Combining style with `onEachFeature` accomplishes something similar to what `pointToLayer` did for the rodents. `style` provides some instructions for how to turn the GeoJSON feature into a map layer, and `onEachFeature` provides some instructions for what to do with that layer.

## Plug in and thin out

The beauty of Leaflet being open source—and of the particular way it's written—is that it's functionality and features can be extended and customized to your heart's content. There's a whole slew of plugins people have written to extend the Leaflet core, most of them very easy to drop right into your project. We'll use a popular one, Leaflet.markercluster. A map cluttered with rat symbols isn't great, and this plugin will help make it more readable and usable. Poke around on the map below.

+-
Leaflet | Tiles by MAPC, Data by MassGIS and City of Boston
View this example on its own

```HTML
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.6.0/dist/leaflet.css" />
<link rel="stylesheet" href="https://unpkg.com/leaflet.markercluster@1.3.0/dist/MarkerCluster.css" />
<link rel="stylesheet" href="https://unpkg.com/leaflet.markercluster@1.3.0/dist/MarkerCluster.Default.css" />

<script src="https://unpkg.com/leaflet.markercluster@1.3.0/dist/leaflet.markercluster.js"></script>
<script src="https://unpkg.com/leaflet@1.6.0/dist/leaflet.js"></script>
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
...

<script>

...

 var rodents =   L.geoJson(data,{
  pointToLayer: function(feature,latlng){
    var marker = L.marker(latlng,{icon: ratIcon});
    marker.bindPopup(feature.properties.Location + '<br/>' + feature.properties.OPEN_DT);
    return marker;
  }
});
 var clusters = L.markerClusterGroup();
clusters.addLayer(rodents);
map.addLayer(clusters);

...

</script>
```

Look at all that added functionality from just a few lines of code! The plugin takes care of figuring out clusters, displaying them, and breaking them apart as you zoom in. (This is just the simplest implementation; there are many options you can explore.)
1.	The first thing we have to do is add the CSS and JavaScript sources for the Marker Cluster library, available from the dist folder of the plugin repository.
2.	In the code, we assign that GeoJSON layer to a rodents variable instead of immediately adding it to the map. Its options remain the same, but we need to save it to a variable in order to use it later.
3.	Next we create a marker cluster layer with `L.markerClusterGroup()`, a function that is part of the plugin, and assign it to a `clusters` variable.
4.	The cluster layer is a group to which we can add individual markers which will then be clustered. Conveniently, we can also just add the entire rodents layer (which is a collection of markers) at once.
5.	Finally, we add the cluster layer to the map, and magically our rodetns are organized into smaller rat armies.

## Let's summarize in a more visual way
But what if we didn't like choropleths or icons, or didn't need to know the location and details of every single rat? Let's add a heatmap! We can use a plugin made for this purpose (Leaflet.heat).

+-
Leaflet | Tiles by MAPC, Data by MassGIS and City of Boston
View this example on its own

```html
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.6.0/dist/leaflet.css" />
<link rel="stylesheet" href="https://unpkg.com/leaflet.markercluster@1.3.0/dist/MarkerCluster.css" />
<link rel="stylesheet" href="https://unpkg.com/leaflet.markercluster@1.3.0/dist/MarkerCluster.Default.css" />

<script src="https://unpkg.com/leaflet.markercluster@1.3.0/dist/leaflet.markercluster.js"></script>
<script src="https://unpkg.com/leaflet@1.6.0/dist/leaflet.js"></script>
<script src="./leaflet-heat.js"></script>
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>

...

<script>

...

$.getJSON("rodents.geojson",function(data){
   var locations = data.features.map(function(rat) {
    var location = rat.geometry.coordinates.reverse();
    location.push(0.5);
    return location;
  });

  var heat = L.heatLayer(locations, { radius: 35 });
  map.addLayer(heat);
});

...

</script>
```

Here we loaded and initialized yet another plugin and used it to compute and display a heatmap
1.	The first thing we did was add the JavaScript source for the Leaflet.heat library, available from the dist folder of the plugin repository.
2.	Then we revisited the `$.getJSON` call that had fetched the `rodents.geojson` data and rendered rat icons. To create a heatmap from the rat data, we needed to have an array of rats, where each rat is represented by an array of latitude, longitude, and intensity.
3.	We made a new variable, called `locations`, and used the array Map function to take all of the rats from the `data.features` array, get the coordinates (in latitude, longitude order... which is the reverse of how it is in the GeoJSON file), and add in a value for intensity since it's required by the plugin. So each rat becomes a `[latitude, longitude, intensity]` array.
4.	We used the plugin to create a new layer for the heatmap, giving it a radius of 35 which affects how it calculates the values between the individual points. Change the value and see what happens!
5.	We added the new heatmap layer to the map

## Go forth
We'll leave it there for now. We've already gone from nothing to a somewhat fancy map, but there is much more you can do with Leaflet. Tinker with what we've done here to better understand how everything works and how different options affect the map. Also think about good map design—this tutorial has only been about technology and hasn't addresssed (or demonstrated) good design. Going forward, have a look at some of these other Leaflet and mapping resources:
•	Tutorials on the main Leaflet site.
•	Leaflet API reference
•	Previews and URLs for many basemap options
•	Mapbox.js, a library that builds on Leaflet and integrates with Mapbox maps
•	The Future of Leaflet (version 1.0 is coming!)
•	How Simplicity Will Save GIS, a talk by Vladimir Agafonkin at FOSS4G 2014, demonstrating the thinking behind Leaflet
•	Dynamic Cartography lab assignments from Penn State, covering many techniques and technologies, including Leaflet.
•	leaflet-omnivore is a particularly handy plugin for loading common data formats such as CSV.
•	Leaflet and Mapbox JavaScript API Fundamentals workshop by Rafa Gutierrez
•	mapschool.io, an on going project explaining map fundamentals
•	Texts such as Web Cartography: Map Design for Interactive and Mobile Devices can be good for understanding good map design principles.
