---
layout: post
title: Using leaflet.js to pan and zoom a big image
date: 2014-06-14 13:49
js: 
  - https://cdn.leafletjs.com/leaflet-0.7.3/leaflet.js
  - /js/2014-06-14-image-map.js
css:
  - https://cdn.leafletjs.com/leaflet-0.7.3/leaflet.css
---

<style>
#image-map {
  width: 100%;
  height: 300px;
  border: 1px solid #ccc;
  margin-bottom: 10px;
}
</style>

I recently used [leaflet.js](https://leafletjs.com/) to embed a large image in a webpage and allow users pan over it and zoom into it. There's a [Stack Overflow](https://stackoverflow.com/questions/13110763/is-leaflet-a-good-tool-for-non-map-images) post about how to do this for a single image, but the anwsers don't have enough detail to make it easy to understand and do.

The image I used was of a newspaper article. It's not big enough to need
segmentation, but it's too big to show on a page at full scale. I need to be
able to read the article text. By using Leaflet I can **zoom out** to get an
overview of the layout, and **zoom in** and **pan** to read the text. Leaflet
takes care of all the user interaction details for me.

**Update 2016-04-05:** [See this gist](https://gist.github.com/longhotsummer/ba9c96bb2abb304e4095ce00df17ae2f) for a full working example HTML file.

## The result

Here's the end product.

<div id="image-map"></div>

and here's the code to make it work.

{% gist longhotsummer/e8b1b195caf0a509f2e4 %}

## How it works

Let's walk through the code.

{% highlight javascript %}
  minZoom: 1,
  maxZoom: 4,
  center: [0, 0],
  zoom: 1,
{% endhighlight %}

We want to be able to zoom over 4 levels (1 to 4). Zoom level 3 is going to be the actual size (1 to 1) of the image.
That means zoom level 4 will be twice as big, zoom level 2 will half as big,
and zoom level 1 a quarter of the original size. We start by focusing on the center of the image, at zoom level 1.

{% highlight javascript %}
  crs: L.CRS.Simple
{% endhighlight %}

This tells Leaflet to use a simple 1-1 mapping between screen pixels and its internal latitude-longitude coordinate system.
In other words, our image is flat, not a globe that we're projecting onto a flat image.

### Image sizes and coordinates

{% highlight javascript %}
// dimensions of the image
var w = 2000,
    h = 1500,
    url = '/images/newspaper-big.jpg';
{% endhighlight %}

This is the size of our image in pixels and its location.

{% highlight javascript %}
// calculate the edges of the image, in coordinate space
var southWest = map.unproject([0, h], map.getMaxZoom()-1);
var northEast = map.unproject([w, 0], map.getMaxZoom()-1);
var bounds = new L.LatLngBounds(southWest, northEast);
{% endhighlight %}

This is the fun bit. We want to tell Leaflet how to place the image on the map.
So we ask it to `unproject` the **pixel coordinates** of the south-west
and north-east corners of the map into **lat-long coordinates**. We tell it to
do that zoom level 3 (or `getMaxZoom()-1`) since at that zoom level, the image
must be actual size and fill the map.

In pixels, Leaflet considers the origin `(0, 0)` to be the top-left corner.
So the bottom left corner has a y-coordinate of `h` and an x-coordinate of `0`.
Similarly, the top right corner has a y-coordinate of `0` and an x-coordinate of `w`.

The `bounds` variable now describes the lat-long coordinates of where on the
map the image must be placed such that it is actual size at zoom level 3.

{% highlight javascript %}
// add the image overlay, 
// so that it covers the entire map
L.imageOverlay(url, bounds).addTo(map);

// tell leaflet that the map is exactly as big as the image
map.setMaxBounds(bounds);
{% endhighlight %}

Finally, we add the image itself as an overlay to the map using the bounds
we just calculated. We then tell Leaflet that the total size of the map is
only as the image, so the user can't drag past the edges.

## Conclusion

The biggest step for me was wrapping my head around Leaflet's coordinate
system and how and when to project from screen pixels into lat-long coordinates
such that my zoom levels worked as expected.

I've found this to be a really powerful way of showing large images in a small page
when users need to be able to get both an overview and see the details. Leaflet
does all the hard work of providing a great-looking interface that the user
is familiar with (provided they've used a mapping website).
