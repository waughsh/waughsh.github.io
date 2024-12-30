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
  const map = L.map('map'); 

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
  }).addTo(map);

  fetch('https://www.waughr.us/images/40under40.geojson')
    .then(response => response.json())
    .then(data => {
      const geojsonLayer = L.geoJSON(data, {
        onEachFeature: function(feature, layer) {
          if (feature.properties && feature.properties.Name) {
            layer.bindPopup("<b>" + feature.properties.Name + "</b><br>" + feature.properties.Time);
          }

          let zoomedIn = false; 

          layer.on('click', function(e) {
            if (zoomedIn) {
              map.fitBounds(geojsonLayer.getBounds()); 
            } else {
              map.setView(e.latlng, 10); 
            }
            zoomedIn = !zoomedIn; 
          });
        }
      }).addTo(map);

      map.fitBounds(geojsonLayer.getBounds()); 

      // Add an image to the top right corner
      L.Control.Watermark = L.Control.extend({
        onAdd: function(map) {
          var img = L.DomUtil.create('img');
          img.src = 'path/to/your/image.png'; // Replace with your image path
          img.style.width = '100px'; // Adjust size as needed
          return img;
        },
        onRemove: function(map) {}
      });

      L.control.watermark = function(opts) {
        return new L.Control.Watermark(opts);
      }

      L.control.watermark({ position: 'topright' }).addTo(map);
    })
    .catch(error => {
      console.error('Error fetching data:', error);
    });
</script>
