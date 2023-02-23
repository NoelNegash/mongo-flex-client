<script lang="ts">
    // OpenLayers
    import {Feature, Map, Overlay, View} from 'ol/index.js';
    import {OSM, Vector as VectorSource} from 'ol/source.js';
    import {Point} from 'ol/geom.js';
    import {Tile as TileLayer, Vector as VectorLayer} from 'ol/layer.js';
    import {useGeographic, transform} from 'ol/proj.js'; 

    // Exports
    export let house = [40.76255897992435, 73.83623652516103];
    // Local state
    let map = null;

    // functions
    const setupMap = (node) => {

    const place = [-house[1], house[0]];
    const point = new Point(place);

    console.log(place)

    const map = new Map({
        target: "2",
        view: new View({
            center: transform(place, 'EPSG:4326', 'EPSG:3857'),
            zoom: 16,
        }),
        layers: [
            new TileLayer({
                source: new OSM(),
            }),
        ],
    });
    }
</script>

<div id="2" class="map" use:setupMap>
</div>

<style>
    .map {
        width: 500px;
        height: 500px;
        display: inline-block;
    }
</style>