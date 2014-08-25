---
layout: post
title: The Men of Merafong City
date: 2014-08-25 20:14
js: 
  - https://maps.googleapis.com/maps/api/js
  - http://cdn.leafletjs.com/leaflet-0.7.3/leaflet.js
  - http://embed.wazimap.co.za/static/js/embed.chart.make.js
---

Merafong City is a local municipality in Gauteng, South Africa, about 80km
southwest of Johannesburg. It encompasses some of the richest gold mines in the world ([Wikipedia](http://en.wikipedia.org/wiki/Merafong_City_Local_Municipality)), and is home to almost 200 thousand individuals.

<iframe id="cr-embed-ward-74804019-demographics-sex_ratio" class="census-reporter-embed" src="http://embed.wazimap.co.za/static/iframe.html?geoID=ward-74804019&chartDataID=demographics-sex_ratio&chartType=pie&chartHeight=200&chartQualifier=&chartTitle=Sex&initialSort=&statType=percentage" frameborder="0" width="30%" height="300" style="float: right; margin: 1em; max-width: 300px;"></iframe>

It also has some of the greatest gender disparity in South Africa.

[Merafong City's population](http://wazimap.co.za/profiles/municipality-GT484/) is 54% male (above the 49% average for South Africa), which seems reasonable for a mining town that employs more male labour than female. The huge gender bias that mining brings to a city is more evident when you dive into the 30-odd electoral wards that the city is broken into.

<div class='with-aside'>
<p>In particular, <a href="http://wazimap.co.za/profiles/ward-74804014/">Ward 14</a> and <a href="http://wazimap.co.za/profiles/ward-74804019/">Ward 19</a> are predominantly men. Ward 19 is 98% male: there are 3 734 men and just 64<sup>&dagger;</sup> women.

<aside>
<sup>&dagger;</sup>Stats SA introduces random errors into census data for small values. The actual count may differ slightly.
</aside>
</p>
</div>

Which made we wonder what the heck was going on in Ward 19. Here's the ward as it appears
in [Google Maps](https://www.google.co.za/maps/place/26%C2%B023'21.2%22S+27%C2%B029'47.9%22E/@-26.3892204,27.4966389,822m/data=!3m1!1e3!4m2!3m1!1s0x0:0x0).
It's a housing compound, complete with a sports stadium and soccer fields.

<div id="map-ward-19" style="width: 100%; height: 300px; margin-bottom: 20px"></div>
<script type="text/javascript">
function loadMaps() {
  map = new google.maps.Map(document.getElementById('map-ward-19'), {
    zoom: 16,
    center: {lat: -26.388723, lng: 27.497634},
    mapTypeId: google.maps.MapTypeId.SATELLITE
  });

  map.data.loadGeoJson('/js/GENDER_Ward_19_(74804019).geojson');
  map.data.setStyle({
    fillColor: 'blue',
    strokeColor: 'red',
  });

  map = new google.maps.Map(document.getElementById('map-ward-15'), {
    zoom: 12,
    center: {lat: -26.360723, lng: 27.497634},
  });

  map.data.loadGeoJson('/js/GENDER_Ward_15_(74804015).geojson');
  map.data.setStyle({
    fillColor: 'blue',
    strokeColor: 'red',
  });
}
window.addEventListener('load', loadMaps);
</script>

Unfortunately the [2011 census](http://wazimap.co.za/profiles/ward-74804019/) has little data beyond basic demographics for this ward, so it's hard to get an accurate picture. We do know that it's predominantly middle-aged black african men. We also know they all have access to running water and flush toilets. Oddly, the census reports all of them having an employment status of "*other/not economically active*".

<iframe id="cr-embed-ward-74804019-demographics-age_group_distribution" class="census-reporter-embed" src="http://embed.wazimap.co.za/static/iframe.html?geoID=ward-74804019&chartDataID=demographics-age_group_distribution&chartType=histogram&chartHeight=200&chartQualifier=&chartTitle=Population+by+age+range&initialSort=&statType=scaled-percentage" frameborder="0" width="100%" height="300" style="margin: 1em; max-width: 720px;"></iframe>

<h4>Why there?</h4>

So there's what looks like a men's residence in Merafong City, which seems reasonable for a mining town. But why did the demarcations board decide to make *that exact area* a ward? It's actually embedded inside [Ward 15](http://wazimap.co.za/profiles/ward-74804015/).

<div id="map-ward-15" style="width: 100%; height: 300px; margin-bottom: 20px"></div>


<h5>Conclusion</h5>

What's going on there? Why does this ward exist, who lives there and what do
they do?

There's a ton of interesting facts like this about South Africa just
waiting to be discovered. These edge cases and outliers are always more
interesting to me than the average trends. Why not take a dive into
[Wazimap](http://wazimap.co.za/) and see what you can find out about where you
live?

If you're interested, [here is a map showing the gender distribution of all wards in Merafong City](http://wazimap.co.za/data/map/?table=GENDER&geo_ids=ward|municipality-GT484&primary_geo_id=ward-74804019#column|Male,sumlev|ward). Notice that two other wards are more than 70% male.
