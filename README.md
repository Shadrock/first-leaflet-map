# Code corrections for Leaflet Rat Maps!

This repository contains outputs and code created using Maptime Boston's fantastic “Boston Ratmap tutorial” which you will be following for this class. You can find [the tutorial here](http://maptimeboston.github.io/leaflet-intro/) and the associated [Github repository for it here](https://github.com/maptimeBoston/leaflet-intro).

It was written by Andy Woodruff, Ryan Mullins and Cristen Jones for [Maptime Boston](https://twitter.com/maptimeboston) and some of the libraries are now out of date, so you'll need to update a few things as you go.

## Remember all that boring stuff in class about maintenance?

As we’ve discussed in class, an important thing to think about when writing code is sustainability. Over time, code gets ‘stale’ and will eventually break if it's not kept up to date. While the Maptime Boston code still works, a few of the libraries have been deprecated by Github and these examples won’t work in Github pages anymore. That’s where this repo comes in: outlined below are the changes you need to make as you read through the tutorial (don’t worry, there are only a few). This repo also contains updated code for all maps in the tutorial. [You see them rendered on this page](https://shadrock.github.io/first-leaflet-map/).

## Replacement No. 1
In the starter code, at the very beginning of the tutorial, are included a stylesheet and library reference that look like this.

```HTML
<link rel="stylesheet" href="./Leaflet-1.0.3/leaflet.css"/>
  <script src="./Leaflet-1.0.3/leaflet.js"></script>
```

These needs to be changed to:

```HTML
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.6.0/dist/leaflet.css" />
  <script src="https://unpkg.com/leaflet@1.6.0/dist/leaflet.js"></script>
```
**What did we change?** We’ve updated the version of Leaflet: in the tutorial it’s version 1.0.3, but we need to upgrade to version 1.6.0 for Github pages to work properly.

## Replacement No. 2
In the section labeled, “Easy! Now let's map our own data!” you’re asked to download a file for jQuery and reference it in your code like this.

```HTML
<script src="jquery-2.1.1.min.js"></script>
```
Change your code to:

```HTML
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
```
**What did we change?** Again, we’ve updated the version for the library. However, we’ve also obviated downloading it and keeping it in our repo, but just referencing it via API!

## Replacement No. 3
In the section labeled, “Plug in and thin out” you’ll use a plugin for clustering points in Leaflet. The lines of code used for the old library in the tutorial are:

```HTML
<link rel="stylesheet" href="./Leaflet.markercluster-1.0.5/MarkerCluster.css"/>
```
and

```HTML
<script src="./Leaflet.markercluster-1.0.5/leaflet.markercluster.js"></script>
```

These should be changed to:

```HTML
<link rel="stylesheet" href="https://unpkg.com/leaflet.markercluster@1.3.0/dist/MarkerCluster.css" />
```
and
```HTML
<link rel="stylesheet" href="https://unpkg.com/leaflet.markercluster@1.3.0/dist/MarkerCluster.Default.css" />
```
**What did we change?** Once again, updating via API versus downloading a file.

## Replacement No. 4 - Last one!
In the section labeled, “Let's summarize in a more visual way” you’ll use another plugin for to create a heatmap Leaflet. As you can guess by now, this plugin is out of date? The old code in the tutorial is:

```HTML
<script src="./Leaflet.heat-0.2.0/leaflet-heat.js"></script>
```

You’ll change this by downloading an updated .js file, which you can find in the [Leaflet-heat Github repo here](https://github.com/Leaflet/Leaflet.heat). Then change your code to reference this. Your code should then be changed to:

```HTML
<script src="./leaflet-heat.js"></script>
```
Woot! You’re updated. Enjoy the tutorial!  

# Optional Improvements for Leaflet Rat Maps!

While I like the tutorial, I think the final output for it could be improved. The changes I made to improve the map are listed below. You can see my final (and hopefully improved!) [Rat Map rendered on this page](https://shadrock.github.io/first-leaflet-map/). You don't _have_ to make these improvements... but your Leaflet coding will get stronger if you do... and your output will look a lot better.

## Changing the Clustering Symbology

I like the functionality of Leaflet's `markercluster` plugin, but I find the symbology strange. After looking into [the documentation available on Github](https://github.com/Leaflet/Leaflet.markercluster) I realized why: the default implementation changes color at bounds of 10 and 100, and uses three _different_ colors to show these changes. This breaks the "[rainbows kill people](https://www.e-education.psu.edu/maps/l5_p5.html)" axiom! Since the markers are showing a difference along a quantitative range, it would be better if we used a chromatic color ramp versus qualitatively different colors.

According to the documentation there are [several ways you can change this](https://github.com/Leaflet/Leaflet.markercluster#customising-the-clustered-markers). My solution was as follows:
1. Download the original `MarkerCluster.Default.css` ([access it here](https://unpkg.com/leaflet.markercluster@1.3.0/dist/MarkerCluster.Default.css)) file and host it locally from my GH pages enabled site, then change the reference URL in the code. Check the code in the `index.html` file of this repo to see that it now looks like this:
```HTML
<link rel="stylesheet" href="https://shadrock.github.io/first-leaflet-map/MarkerCluster.Default.css" />
```
2. Next I changed the color codes in the `MarkerCluster.Default.css` file in this repo. I used [ColorBrewer](https://colorbrewer2.org) to pick a single hue color scheme for sequential data (the clusters are changing based on the number of points). I choose 4 classes, used the lightest one as the "ring" around each cluster marker and made the interior of the clusters progressively darker the more observations they grouped.

Now you've got appropriately symbolized cluster markers!

## Map Elements for Improved User Experience

It's easy to forget that, after following the tutorial to build your map, you automatically understand the symbology: but would somebody who's viewing your map for the first time understand it? The tutorial enables popups for each of the neighborhoods, but with popups already enabled on our points, this might be confusing or overwhelming to the user. While I kept the pop ups enabled, I added a legend that contains the information the user will need to know about the data, colors, and classifications. The tutorial displays point data that shows up as either a rat icon or a cluster marker: neither of which are explained. For good measure, I added a scale bar in the lower left-hand corner of the map for users who aren't familiar with the scale of Boston.

### Adding a Legend
Add a legend by creating a Leaflet control, setting the control to populate with HTML that represents the legend components, and styling the HTML with CSS so they render properly. Enter the following block of code **in between** the `script` tags of your HTML document.

```JavaScript
// Create Leaflet Control Object for Legend
var legend = L.control({position: 'topright'});

// Function that runs when legend is added to map
legend.onAdd = function (map) {

  // Create Div Element and Populate it with HTML
  var div = L.DomUtil.create('div', 'legend');
  div.innerHTML += '<b>Rat Density per sq. mi.</b><br />';
  div.innerHTML += '<b>by Neighborhood</b><br/>';
  div.innerHTML += '<i style="background: #993404"></i><p>> 80</p>';
  div.innerHTML += '<i style="background: #d95f0e"></i><p>> 40</p>';
  div.innerHTML += '<i style="background: #fe9929"></i><p>> 20</p>';
  div.innerHTML += '<i style="background: #fed98e"></i><p>> 10</p>';
  div.innerHTML += '<i style="background: #ffffd4"></i><p>> 0</p>';
  div.innerHTML += '<i style="background: #f7f7f7"></i><p>No Data</p>’;
  div.innerHTML += '<hr><img src="images/rat.png"><p>Rat sighting.</p>';
  div.innerHTML += '<img src="images/clusters.png"><p>Grouped rat sightings.</p>';

  // Return the Legend div containing the HTML content
  return div;

};

// Add Legend to Map
legend.addTo(map);
```

In the code above, we created an instance of a [Leaflet Control object](https://leafletjs.com/reference-1.7.1.html#control), calling it `legend`, and used the position option to place it in the `'topright'` of our map. Next, we used the `onAdd` method of the control to run a function when the legend is added. That function creates a new `div`, giving it a class of `legend`. This will let us use CSS to style everything using the `legend` tag. In the newly created div, we populated it with HTML by using a built-in JavaScript method called `innerHTML`. Using innerHTML allows us to change the content of the HTML and add it to the legend div. Using the plus-equal (`+=`) instead of equal (`=`) is the syntax for "append." Everytime this is used, code following it is appended to existing code. We use this to write the HTML we want to use in our legend. Note, the `i` tag represents our legend icons. Within the HTML, fill in the colors and ranges so that they match our data classification. After the HTML is appended, return the `div` element. Lastly, add the legend to the map.

Notice that I reference two images in the legend: the `rat.png` from the tutorial (in our `images` folder) and a `clusters.png`, which is a screenshot of a cluster marker that I cleaned up a bit and added to the `images` folder.

Now we have to add the CSS code to style the legend. If you save and refresh without doing this, the items you see won't make much sense since you need to use CSS to give them placement and organization! The following CSS code will style our elements. Enter it between the `style` tags in the head of your HTML document.

```CSS
.legend {
	line-height: 18px;
	color: #333333;
	font-family: 'Open Sans', Helvetica, sans-serif;
	padding: 6px 8px;
	background: white;
	background: rgba(255,255,255,0.8);
	box-shadow: 0 0 15px rgba(0,0,0,0.2);
	border-radius: 5px;
}

.legend i {
	width: 18px;
	height: 18px;
	float: left;
	margin-right: 8px;
	opacity: 0.7;
}

.legend img {
	width: 18px;
	height: 18px;
	float: left;
}

.legend p {
	font-size: 12px;
	line-height: 18px;
	margin: 0;
}
```

First, we set properties for the legend as a whole by using `.legend` to style the legend class. We set a line height, color, font, padding, background, drop shadow, and border corner radius. Next we set our icon (`i`) tag, this should be set to `float: left;` so that elements will align into columns, then we set properties for the image (`img`) tag, making them the same size and giving them the same float as the icons. Lastly, we style our paragraph tag (`p`), making sure line-height is consistent with the others. Save and refresh your map. You should see your styled legend applied to your map.

An alternative method to create a legend uses a For loop conditional statement to add legend elements based on the number of data classification categories you have. There is a "legend" section of [the Leaflet choropleth tutorial that shows this](https://leafletjs.com/examples/choropleth/#custom-legend-control).

### Adding a Scale Bar
Although I don't often add scale bars to web maps where the geography is really familiar to most users, this map implies (and expects) a user to zoom in to see specific rat sightings. Because some users may not be intimately familiar with the scale of Boston, I added a legend using the following code:

```JavaScript
// Add Scale Bar to Map
L.control.scale({position: 'bottomleft'}).addTo(map);
```
And that's it: now we have a vastly improved Rat Map!
