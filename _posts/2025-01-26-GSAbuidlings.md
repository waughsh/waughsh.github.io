---
layout: post
title: "GSA Buidlings Map"
date: 2025-1-26 10:00:00 -0500
categories: news
published: true 
---
<link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
<link rel="stylesheet" href="https://unpkg.com/leaflet.markercluster@1.5.3/dist/MarkerCluster.css" />
<link rel="stylesheet" href="https://unpkg.com/leaflet.markercluster@1.5.3/dist/MarkerCluster.Default.css" />
<link rel="stylesheet" href="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.css" />
<script src="https://unpkg.com/leaflet-control-geocoder/dist/Control.Geocoder.js"></script>
<script src="https://unpkg.com/leaflet.markercluster@1.5.3/dist/leaflet.markercluster.js"></script>

<div style="display: flex; align-items: flex-start;">
  <img src="https://waughr.us/images/4040-color-vert-RGB.png" alt="Image Description" style="width: 200px; height: auto; margin-right: 20px; float: left;"> 

</div>

<div id="map" style="height: 400px;"></div>

<script>
  const map = L.map('map'); 

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
  }).addTo(map);

  fetch('https://www.waughr.us/images/Maryland_GSA.geojson')
    .then(response => response.json())
    .then(data => {
      // Cluster the markers
      const markers = L.markerClusterGroup(); 
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
      });
      markers.addLayer(geojsonLayer); 
      map.addLayer(markers); 

      map.fitBounds(markers.getBounds()); 

      // Add an image to the top right corner
      L.Control.Watermark = L.Control.extend({
        onAdd: function(map) {
          var img = L.DomUtil.create('img');
          img.src = 'https://waughr.us/images/4040-color-vert-RGB.png'; 
          img.style.width = '50px'; 
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
  L.Control.geocoder().addTo(map);
</script>
