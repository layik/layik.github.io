---
permalink: /jspromise.html
---
[Home](https://layik.github.io) | About | Current
<hr/>

28th Mar 2019

# *wip*

Will add tips and snippets related to the use of LeafletJS.

## React Leaflet
Setting up React leaflet should be easy. The only difficulty or not so clear area is including the required CSS and LeafletJS itself depending on how your project is setup or the way you want to set it up.

## Highlighting a polygon
 One of the functionalities of LeafletJS I discover recently is polygon highlighting. In Leaflet terms, layer highlighting.

Although we are using the experimental "preferCanvas" tag,Leaflet still supports the highlighting feature. We are of course, using [`react-leaflet`](https://github.com/PaulLeCam/react-leaflet). The wrapper react library has a component called [GeoJSON](https://github.com/PaulLeCam/react-leaflet/blob/master/src/GeoJSON.js) which is a React way of adding a LeafletElement/layer to the underlying Leaflet map.

To the code now, in order to add a `mouseover` and `mouseout` events to the layer, one can use the "onEachFeature" function of the GeoJSON component as follows:

```js
//Map is the react-leaflet component
<Map
    zoom={zoom}
    ref='map'
    center={center}
    >
    <GeoJSON
        style={defaultStyle}
        key={key}
        data={feature} onEachFeature={(feature, layer) => 
        //the `layer`
        {
            feature.properties && feature.properties.flow &&
                layer.bindPopup("A popup!");
                layer.on("mouseover", function (e) {
                    layer.setStyle(highlightStyle);
                    layer.openPopup();
                })
                layer.on("mouseout", function (e) {
                    layer.setStyle(defaultStyle);
                    layer.closePopup();
                })
        }} 
    />
</Map>
```
Credit and thanks to this [tutorial](http://palewi.re/posts/2012/03/26/leaflet-recipe-hover-events-features-and-polygons/).
