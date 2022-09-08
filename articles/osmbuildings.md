---
permalink: /osmbuildings.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

8th Sep 2022

OSM includes building models with an API that can return a GeoJSON format of them if an X,Y tile ID is provided. There is a Leaflet [plugin](https://osmbuildings.org/) which inspired this work.

The [TGVE](https://github.com/tgve/tgvejs) is built with these sorts of datasets in mind, therefore it also provided the reason to add, as it was always going to happen, some API callback functions to the core of the TGVE so that the TGVE can be used to create a webapp to explore OSM building data.

The React code to use TGVE, with very sharp edges, can be as simple as following:

```js
import React, { useState } from 'react';
import Tgve from '@tgve/tgvejs';

import './App.css';
const lon = 13.40438, lat = 52.51836, zoom = 15;

function App() {
  const [vs, setVs] = useState({
    longitude: lon,
    latitude: lat,
    zoom
  })
  const [url, setURL] = useState(generateURL())
  const [data, setData] = useState(null)

  return (
    <Tgve
      onViewStateChange={({ viewState }) => {
        if (viewState) {
          setVs(viewState)
          const newURL = generateURL()
          url !== newURL
          && fetch(newURL)
            .then(response => response.ok && response.json())
            .then(data => {
              // console.log(url, newURL)

              setURL(newURL)
              setData(data)
            });
        }

      }}
      data={data}
      defaultURL={url}
       />
  );

  function generateURL() {
    const z = Math.floor(vs.zoom) < zoom ? zoom : Math.floor(vs.zoom);
    const x = lon2tile(vs.longitude, z);
    const y = lat2tile(vs.latitude, z);

    const tileURL = `https://data.osmbuildings.org/0.2/anonymous/tile/${z}/${x}/${y}.json`;
    return tileURL;
  }
}

/**
 *
 * Function from OSM API documentations
 */

function lon2tile(lon, zoom) { return (Math.floor((lon + 180) / 360 * Math.pow(2, zoom))); }
function lat2tile(lat, zoom) { return (Math.floor((1 - Math.log(Math.tan(lat * Math.PI / 180) + 1 / Math.cos(lat * Math.PI / 180)) / Math.PI) / 2 * Math.pow(2, zoom))); }

export default App;
```

The above is a very simple component that keeps updating a TGVE instance with OSM data using TGVE version 1.5.1 callback, namely `onViewStateChange`. As the TGVE returns the current viewport of the map, we can generate an OSM tile URL that we can fetch building GeoJSON data from and in turn feed the data back to the TGVE application.

The result would be something like:
<img src="https://user-images.githubusercontent.com/408568/189155598-d4809604-6f6d-4863-82b6-34721d12c190.png" width="100%">
