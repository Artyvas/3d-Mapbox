<template>
  <div class="about">
    <h1>This is my map of Denali National Park in Alaska</h1>

<div id="map"></div>

  </div>
</template>

<style>
#map {
  width: 100%;
  height: 300px;
}

#marker {
background-image: url('https://docs.mapbox.com/mapbox-gl-js/assets/washington-monument.jpg');
background-size: cover;
width: 50px;
height: 50px;
border-radius: 50%;
cursor: pointer;
}
 
.mapboxgl-popup {
max-width: 200px;
}
</style>

<script>
/* global mapboxgl */

export default {
  data() {
    return {
      value: null,
      places: [
        { lat: 63.07002, lng: -151.00733, description: 'The summit of Denali!'},
        { lat: 62.95146, lng: -151.08944, description: 'The summit of Hunter'},
        { lat: 62.96126, lng: -151.39823, description: "Foraker - also a preeeetty sick Alaskan summie!"}
      ]
    };
  },
  mounted: function() {
    this.setupMap();
  },
  methods: {
    setupMap: function() {
      mapboxgl.accessToken = process.env.VUE_APP_MY_API_KEY;
        var map = new mapboxgl.Map({
          container: 'map', // container id
          style: 'mapbox://styles/mapbox/satellite-v9', // style URL
          center: [this.places[0].lng, this.places[0].lat], // starting position [lng, lat]
          zoom: 8.5, // starting zoom
          pitch: 85,
          bearing: 80, 
          });
          
     map.on('load', function () {
            var layers = map.getStyle().layers;
 
                var labelLayerId;
                      for (var i = 0; i < layers.length; i++) {
                      if (layers[i].type === 'symbol' && layers[i].layout['text-field']) {
                      labelLayerId = layers[i].id;
                      break;
                      }
                      }

                map.addLayer(
                      {
                      'id': '3d-buildings',
                      'source': 'composite',
                      'source-layer': 'building',
                      'filter': ['==', 'extrude', 'true'],
                      'type': 'fill-extrusion',
                      'minzoom': 15,
                      'paint': {
                      'fill-extrusion-color': '#aaa',

                      // use an 'interpolate' expression to add a smooth transition effect to the
                      // buildings as the user zooms in
                      'fill-extrusion-height': [
                      'interpolate',
                      ['linear'],
                      ['zoom'],
                      15,
                      0,
                      15.05,
                      ['get', 'height']
                      ],
                      'fill-extrusion-base': [
                      'interpolate',
                      ['linear'],
                      ['zoom'],
                      15,
                      0,
                      15.05,
                      ['get', 'min_height']
                      ],
                      'fill-extrusion-opacity': 0.6
                      }
                      },
                labelLayerId
                );
            
            map.addSource('mapbox-dem', {
                'type': 'raster-dem',
                'url': 'mapbox://mapbox.mapbox-terrain-dem-v1',
                'tileSize': 512,
                'maxzoom': 14
                });
                // add the DEM source as a terrain layer with exaggerated height
            map.setTerrain({ 'source': 'mapbox-dem', 'exaggeration': 1.5 });
            
            // add a sky layer that will show when the map is highly pitched
            map.addLayer({
                'id': 'sky',
                'type': 'sky',
                'paint': {
                'sky-type': 'atmosphere',
                'sky-atmosphere-sun': [0.0, 0.0],
                'sky-atmosphere-sun-intensity': 5
                }
                });
                });

          this.places.forEach(function(place) {
            var popup = new mapboxgl.Popup({ offset: 25 }).setText(
                  place.description
                  );
            var marker = new mapboxgl.Marker()
                .setLngLat([place.lng, place.lat])
                .setPopup(popup)
                .addTo(map);
                console.log(marker)
          });
                console.log(map);
    },
  },
};
</script>