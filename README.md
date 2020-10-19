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
