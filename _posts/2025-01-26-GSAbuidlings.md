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


<div id="map" style="height: 400px;"></div>

<script>
  const map = L.map('map'); 

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
  }).addTo(map);

  fetch('https://www.waughr.us/images/GSA_buildings.geojson')
    .then(response => response.json())
    .then(data => {
      // Cluster the markers
      const markers = L.markerClusterGroup(); 
      const geojsonLayer = L.geoJSON(data, {
        onEachFeature: function(feature, layer) {
          if (feature.properties && feature.properties.City) {
            layer.bindPopup("<b>" + feature.properties.City + "</b><br>" + feature.properties.County + feature.properties.Address + feature.properties.'Lessor Name' );
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
          img.src = 'http://waughr.us/images/image4512.png'; 
          img.style.width = '50px'; 
          return img;
        },
        onRemove: function(map) {}
      });

      L.control.watermark = function(opts) {
        return new L.Control.Watermark(opts);
      }

      L.control.watermark({ position: 'topright' }).addTo(map);

      // Add the geocoder control with custom zoom behavior and flashing
      const geocoder = L.Control.geocoder({
        defaultMarkGeocode: false
      })
      .on('markgeocode', function(e) {
        const result = e.geocode;
        const resultLatLng = result.center;

        let nearestDistance = Infinity;
        let nearestPoint;
        let nearestPointMarker; // To store the nearest point's marker

        L.geoJSON(data, {
          onEachFeature: function(feature, layer) {
            const distance = resultLatLng.distanceTo(layer.getLatLng());
            if (distance < nearestDistance) {
              nearestDistance = distance;
              nearestPoint = layer.getLatLng();
              nearestPointMarker = layer; // Store the nearest marker
            }
          }
        });

        const zoomLevel = nearestDistance > 500000 ? 4 : 
                         nearestDistance > 100000 ? 6 : 
                         nearestDistance > 10000 ? 8 : 12; 

        // Flash the nearest point
        const flashInterval = setInterval(function() {
          nearestPointMarker.setOpacity(nearestPointMarker.getOpacity() === 1 ? 0 : 1);
        }, 500);

        setTimeout(function() {
          clearInterval(flashInterval);
          nearestPointMarker.setOpacity(1);
        }, 2000); 

        map.fitBounds(L.latLngBounds(resultLatLng, nearestPoint), {
          maxZoom: zoomLevel
        });
      })
      .addTo(map);
    })
    .catch(error => {
      console.error('Error fetching data:', error);
    });
</script>
