---
layout: post
title: Dissertation Defense is upcoming!
date: 2018-02-28 13:31
author: waughsh
comments: true
categories: [Uncategorized]
---
<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>

I will be defending my dissertation titled:
<p style="text-align:center;"><span style="text-decoration:underline;"><strong>Investigating Spatial Dynamics of Zoonoses between Animal and Human Populations: a </strong></span><span style="text-decoration:underline;"><strong>One-Health Perspective </strong></span></p>
(See Below)

<img class="alignnone size-full wp-image-198" src="https://waughsh.files.wordpress.com/2018/02/epi-presentation-announcement-poster-waugh_4.png" alt="Epi Presentation Announcement Poster-Waugh_4" width="500" height="600" />


<div id="map" style="height: 400px;"></div>

<script>
  var map = L.map('map').setView([37.8, -96], 4); // Set initial view and zoom
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
  }).addTo(map);

  fetch('https://waughr.us/images/locations.geojson') // Fetch the GeoJSON data
    .then(response => response.json())
    .then(data => {
      L.geoJSON(data, {
        onEachFeature: function (feature, layer) {
          if (feature.properties && feature.properties.name) {
            layer.bindPopup("<b>" + feature.properties.name + "</b><br>" + feature.properties.description);
          }
        }
      }).addTo(map);
    });
</script>
