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
      const markers = L.markerClusterGroup(); 
      const geojsonLayer = L.geoJSON(data, {
        onEachFeature: function(feature, layer) {
          if (feature.properties) {
            // Construct popup content with City, County, and Lessor Name
            let popupContent = "";
            if (feature.properties.City) {
              popupContent += "<b>City:</b> " + feature.properties.City + "<br>";
            }
            if (feature.properties.County) {
              popupContent += "<b>County:</b> " + feature.properties.County + "<br>";
            }
            if (feature.properties["Lessor Name"]) { 
              popupContent += "<b>Lessor Name:</b> " + feature.properties["Lessor Name"];
            }
            layer.bindPopup(popupContent);
          }

          // Enable popups on hover
          layer.on('mouseover', function(e) {
            layer.openPopup();
          });
          layer.on('mouseout', function(e) {
            layer.closePopup();
          });

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

      const geocoder = L.Control.geocoder({
        defaultMarkGeocode: false
      })
      .on('markgeocode', function(e) {
        const result = e.geocode;
        const resultLatLng = result.center;

        let nearestDistance = Infinity;
        let nearestPoint;
        let nearestPointMarker; 

        L.geoJSON(data, {
          onEachFeature: function(feature, layer) {
            const distance = resultLatLng.distanceTo(layer.getLatLng());
            if (distance < nearestDistance) {
              nearestDistance = distance;
              nearestPoint = layer.getLatLng();
              nearestPointMarker = layer; 
            }
          }
        });

        const zoomLevel = nearestDistance > 500000 ? 4 : 
                         nearestDistance > 100000 ? 6 : 
                         nearestDistance > 10000 ? 8 : 12; 

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
