<!DOCTYPE html>
<html lang="en">

<head>
    <title>India</title>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <!-- Ajax -->
    <script src="jquery.min.js"></script>
    <!-- Latest compiled and minified CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css" integrity="sha384-HSMxcRTRxnN+Bdg0JdbxYKrThecOKuH5zCYotlSAcp1+c8xmyTe9GYg1l9a69psu" crossorigin="anonymous">

    <!-- Optional theme -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap-theme.min.css" integrity="sha384-6pzBo3FDv/PJ8r2KRkGHifhEocL+1X2rVCTTkUfGk7/0pbek5mMa1upzvWbrUbOZ" crossorigin="anonymous">

    <!-- Latest compiled and minified JavaScript -->
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js" integrity="sha384-aJ21OjlMXNL5UyIl/XNwTMqvzeRMZH2w8c5cRVpzpU8Y5bApTppSuUkhZXN0VxHd" crossorigin="anonymous"></script>

    <!-- AwesomeFonts -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css" integrity="sha384-wvfXpqpZZVQGK6TAh5PVlGOfQNHSoD2xbE+QkPxCAFlNEevoEH3Sl0sibVcOQVnN" crossorigin="anonymous">

    <link rel="stylesheet" href="leaflet.css">
    <script src="./leaflet.js"></script>
    <style>
        body {
            padding: 0;
            margin: 0;
        }
        
        html,
        body,
        #map {
            height: 100%;
            width: 100vw;
        }
        
        .info {
            width: max-content;
            font-size: medium;
            text-align: center;
        }
    </style>
</head>

<body>
    <h1> Using Leaflet Js and mapbox api </h1>
    <div id="map" class="col-sm-12 col-md-12 col-lg-6"></div>
</body>
<script src="./india_states_2019.js"></script>
<script src="./corona.js"></script>
<script>
    var map = L.map('map').setView([23.32208, 77.958984], 5);

    L.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw', {
        maxZoom: 18,
        attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, ' +
            '<a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, ' +
            'Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
        id: 'mapbox/light-v9',
        tileSize: 512,
        zoomOffset: -1
    }).addTo(map);
    L.geoJson(indiaState19).addTo(map);

    function getColor(d) {
        return d > 120000 ? 'rgb(255, 81, 0)' :
            d > 100000 ? 'rgb(211, 90, 35)' :
            d > 80000 ? 'rgb(150, 83, 52)' :
            d > 50000 ? 'rgb(107, 72, 56)' :
            d > 40000 ? 'rgb(146, 91, 65)' :
            d > 30000 ? 'rgb(102, 76, 64)' :
            d > 20000 ? 'rgb(94, 100, 70)' :
            d > 10000 ? 'rgb(126, 139, 76)' :
            d > 8000 ? 'rgb(150, 170, 68)' :
            d > 5000 ? 'rgb(183, 253, 31)' :
            d > 4000 ? 'rgb(188, 231, 17)' :
            d > 3000 ? 'rgb(141, 175, 3)' :
            d > 2000 ? 'rgb(76, 138, 34)' :
            d > 1000 ? 'rgb(89, 194, 18)' :
            'rgb(102, 240, 11)';
    }

    function style(feature) {
        var active = corona[feature.properties.ST_ID.toString()].active;
        return {
            fillColor: getColor(active),
            weight: 2,
            opacity: 1,
            color: 'white',
            dashArray: '2',
            fillOpacity: 0.4
        };
    }

    L.geoJson(indiaState19, {
        style: style,
    }).addTo(map);


    function highlightFeature(e) {
        var layer = e.target;
        info.update(layer.feature.properties);
        layer.setStyle({
            weight: 2,
            color: 'black',
            fillColor: 'gray',
            dashArray: '',
            fillOpacity: 0.0,
        });

        if (!L.Browser.ie && !L.Browser.opera && !L.Browser.edge) {
            layer.bringToFront();
        }
    }

    function resetHighlight(e) {
        geojson.resetStyle(e.target);
        info.update();
    }

    function zoomToFeature(e) {
        map.fitBounds(e.target.getBounds());
    }

    function onEachFeature(feature, layer) {
        layer.bindPopup('<div class="info"><h4>' + feature.properties.ST_NM + '</h4><h5 class="text-danger">Active-Cases <i class="fa fa-bug" aria-hidden="true"></i> : ' + corona[feature.properties.ST_ID].active + '</h5><h5 class="text-success">Recovered Cases<i class="fa fa-plus" aria-hidden="true"></i> :' + corona[feature.properties.ST_ID].recovered +
            '</h5><h5 class="text-light">Deatch <i class="fa fa-h-square" aria-hidden="true"></i> : ' + corona[feature.properties.ST_ID].deaths + '</h5></div>');
        layer.on({
            // mouseover: highlightFeature,
            // mouseout: resetHighlight,
            // click: zoomToFeature,
            // click: addpop,
        });
    }

    geojson = L.geoJson(indiaState19, {
        style: style,
        onEachFeature: onEachFeature
    }).addTo(map);
</script>

</html>