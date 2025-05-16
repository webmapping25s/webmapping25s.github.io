# Session 8: Wind & Wetter Beispiel

## Repo vorbereiten

- Repo `forecast` auf github.com erstellen

- lokal klonen, Template <https://webmapping.github.io/templates/forecast.zip> auspacken, `add` und `push` wie gehabt

    **COMMIT**: <https://github.com/webmapping/forecast/commit/9955bb4e9f4fe7e83f6813a2691acf341e4f2816>

- Repo über die *Settings* bei  github.com Online stellen - <https://webmapping.github.io/forecast> ist ab jetzt verfügbar

- was ist neu im Template? Wetter-Icons im Format SVG (Scalable Vector Graphics) in einem Unterverzeichnis ` icons/`. Quelle ist  <https://github.com/metno/weathericons/> und heruntergeladen wurden sie über die *Download ZIP* Option bei der Repo-Startseite (Button "*&lt;> Code*")

- die Links gehen zu

    - MET Norway Locationforecast: <https://api.met.no/weatherapi/locationforecast/2.0/documentation>

    - MET Norway Vorhersage Innsbruck: <https://api.met.no/weatherapi/locationforecast/2.0/compact?lat=47.267222&lon=11.392778>

    - MET Weather API icons : <https://github.com/metno/weathericons/>

    - Nominatim Reverse Geocoding: <https://nominatim.org/release-docs/latest/api/Reverse/>

    - Nominatim Reverse Geocoding Innsbruck: <https://nominatim.openstreetmap.org/reverse?lat=47.267222&lon=11.392778&zoom=15&format=jsonv2>

    - ECMWF (European Centre for Medium-Range Weather Forecasts) Open Data: <https://www.ecmwf.int/en/forecasts/datasets/open-data>

Wir werden uns zuerst mit dem MET Norway Locationforecast beschäftigen und beginnen mit einem Map-Event


## Wettervorhersage met.no implementieren

Im ersten Schritt werden wir für jeden Ort der Welt eine 24 Stunden Vorhersage mit den Daten von MET Norway implementieren. Dazu müssen wir:

- einen Event-Listener definieren, der auf Kartenklicks reagiert
- nach dem Laden diesen Event-Listener auslösen indem wir eine Klick auf Innsbruck simulieren
- bei jedem Klick JSON-Daten für die Wettervorhersage des angeklickten Punkts von MET Norway downloaden
- ein Popup zur Visualisierung der JSON-Daten am angeklickten Punkt öffnen 

### a) Event-Listener für Kartenklicks und Klick auf Innsbruck simulieren

Über den Leaflet Event-Handler `map.on` (<https://leafletjs.com/reference.html#evented-on>) können wir auf Events in der Karte reagieren. Die Liste der möglichen Events auf die wir reagieren können ist lange (<https://leafletjs.com/reference.html#map>) - wir wählen "click" als erstes Argument beim Aufruf von `map.on`. Als zweites Argument erwartet `map.on` eine Funktion, der automatisch ein Event-Objekt übergeben wird. In diesem Objekt finden wir die Koordinaten des angeklickten Punkt in `evt.latlng` wieder. Wir lassen sie uns in der Konsole anzeigen:

```javascript
// auf Kartenklick reagieren
map.on("click", function(evt){
    console.log(evt.latlng);
});
```

**COMMIT**:<https://github.com/webmapping/forecast/commit/17d7ee10c3301e2f8c82faf9ad913cb3a5bf1a23>

Über `map.fire` (<https://leafletjs.com/reference.html#evented-fire>) können wir einen Klick auf die Karte simulieren und dabei selbst eine beliebige Koordinate als `latlng` Objekt übergeben. Wir wählen die Koordinaten von Innsbruck, simulieren damit nach dem Laden der Karte einen Klick auf Innsbruck und lösen so auch den Event-Listener `map.on` aus.

```javascript
// Klick auf Innsbruck simulieren
map.fire("click", {
    latlng: {
        lat: ibk.lat,
        lng: ibk.lng,
    }
});
```

**COMMIT**:<https://github.com/webmapping/forecast/commit/bef0bd451ca7f17b3bd6972c0d898498f43c3fd2>

Damit können wir nach dem Laden der Karte die Wettervorhersage für Innsbruck anzeigen und bei jedem Klick auf die Karte zur Vorhersage des angeklickten Orts wechseln.

### b) Met.no Daten asynchron laden

Den Code zum Laden und Visualisieren der Daten verpacken wir in eine asynchrone Funktion `showForecast()`. Sie erwartet die Koordinate des gewünschten Punkts als `latlng` Objekt und wird bei jedem (auch simulierten) Klick aus `map.on` heraus aufgerufen.

```javascript
// MET Norway Vorhersage Visualisieren
async function showForecast(latlng) {
    console.log("Popup erzeugen bei: ", latlng);
}

// auf Kartenklick reagieren
map.on("click", function(evt){
    showForecast(evt.latlng);
});

```

**COMMIT**: <https://github.com/webmapping/forecast/commit/cfc3678f7f8aa15382ddd75cd0de94ef8f8fc372>

In der Funktion definieren wir die URL für den API-Aufruf bei MET Norway in einer Variablen `url` und speichern die JSON-Daten wie gewohnt über *await fetch* und *await response* in einer Variablen `jsondata`. 

```javascript
// MET Norway Vorhersage Visualisieren
async function showForecast(latlng) {
    let url = `https://api.met.no/weatherapi/locationforecast/2.0/compact?lat=${latlng.lat}&lon=${latlng.lng}`;
    let response = await fetch(url);
    let jsondata = await response.json();
    console.log(jsondata)
}
```

**COMMIT**: <https://github.com/webmapping/forecast/commit/e921891e849ed2bee5bcde2ef05416dffe91dbde>

Damit stehen in der Variablen `jsondata` alle Vorhersage-Daten für den gewünschten Punkt zur Verfügung und es wird Zeit, sich diese JSON-Daten genauer anzusehen. Was suchen wir dort?

- den Zeitpunkt der Vorhersage
- aktuelle Wetterwerte
- den Ausblick auf die nächsten 24 Stunden

Wir öffnen den Link im Footer des Templates *MET Norway Vorhersage Innsbruck* (<https://api.met.no/weatherapi/locationforecast/2.0/compact?lat=47.267222&lon=11.392778>) in einem neuen Tab und lassen uns die Daten für Innsbruck (formatiert vom Browser) anzeigen. Dort erkennen wir die üblichen Attribute wie `geometry` und `properties`. Die Geometrie folgt dem alt bekannten Muster, aber die Properties sehen ganz anders aus - sie bestehen aus einem verschachtelten Objekt, das wir uns näher ansehen müssen.

Wir sehen ein JSON-Objekt vom `type`: `Feature` mit:

- Koordinaten und Seehöhe im Array `jsondata.geometry.coordinates`

- Zeitstempel der Erstellung der Vorhersage in `jsondata.properties.meta.updated_at`

- Einheiten der verfügbaren Attribute in `jsondata.properties.meta.units`

- Vorhersage-Daten als Objekte im Array `jsondata.properties.timeseries`

    - insgesamt gibt es 90 Vorhersage-Zeitpunkte für 9 Tage in die Zukunft

        - 1-stündlich: `jsondata.properties.timeseries[0]` - `jsondata.properties.timeseries[62]`

        - 6-stündlich: `jsondata.properties.timeseries[62]` - `jsondata.properties.timeseries[89]`

    - jeder Eintrag dieses Arrays besitzt:

        - einen Zeitstempel `time`

        - die vorhergesagten Werte in `data.instant.details`

        - sowie Wetteraussichten mit Wettersymbolen und voraussichtlichen Niederschlagsmengen für die drei Zeitpunkte `data.next_1_hours` (nur bei 1-stündlichen Datensätzen), `data.next_6_hours`, `data.next_12_hours`

### c) Popup für die Wettervorhersage

Wir beginnen mit dem Popup und erzeugen in einer Variablen `markup` den HTML Code für eine ungeordnete Liste mit deutschsprachigen Labeln und den Namen der Attribute in `jsondata.properties.meta.units`. Das Auflösen mit tatsächlichen Werten kommt später.

```javascript
let markup = `
    <ul>
        <li>Luftdruck (hPa): air_pressure_at_sea_level</li>
        <li>Lufttemperatur (°C): air_temperature</li>
        <li>Bewölkungsgrad (%): cloud_area_fraction</li>
        <li>Luftfeuchtigkeit (%): relative_humidity</li>
        <li>Windrichtung (°): wind_from_direction</li>
        <li>Windgeschwindigkeit (km/h): wind_speed</li>
    </ul>
`;
```

Den Niederschlag lassen wir weg, denn er wird zwar in den Metadaten angeführt, fehlt aber in den Detaildaten. Nur bei den Wetteraussichten mit Wettersymbolen und voraussichtlichen Niederschlagsmengen in `data.next_1_hours`, `data.next_6_hours`, `data.next_12_hours` wäre er zu finden. Ob es regnet oder nicht, werden wir später noch im Popup anzeigen ...

Dann öffnen wir über `L.popup` (<https://leafletjs.com/reference.html#popup>) bei der angeklickten Koordinate ein **standalone Popup** und schreiben es in das Overlay `overlays.forecast`.

**Achtung**: leider wird an dieser Stelle das `latlng` Objekt nicht richtig umgesetzt - wir bekommen einen Fehler in der Konsole und müssen lat/lng als Array definieren:

```javascript
L.popup([latlng.lat, latlng.lng], {
    content: markup
}).openOn(overlays.forecast);
```

**COMMIT**: <https://github.com/webmapping/forecast/commit/beb56d2c04bd19abf7d5c4263c98a55963218c81>


Zum Umsetzen der Attribut-Namen in tatsächliche Werte müssen wir uns die Daten genauer ansehen. Die aktuellen Wetterwerte finden wir im ersten Eintrag (Index `0`) des `jsondata.properties.timeseries` Array und dort in `jsondata.properties.timeseries[0].data.instant.details` - ein *shortcut* zu diesen tief verschachtelten Details bietet sich an - wir definieren ihn vor der Variablen `markup` als Variable `details`:

```javascript
let details = jsondata.properties.timeseries[0].data.instant.details;
```

Danach ersetzen wir in der Variablen `markup` mit Template-Syntax die aktuellen Wetterwerte. Den Wind rechnen wir in km/h um und runden mit `Math.round()` auf ganze Zahlen

```javascript
let details = jsondata.properties.timeseries[0].data.instant.details;
let markup = `
    <ul>
        <li>Luftdruck (hPa): ${details.air_pressure_at_sea_level} </li>
        <li>Lufttemperatur (°C): ${details.air_temperature}</li>
        <li>Bewölkungsgrad (%): ${details.cloud_area_fraction}</li>
        <li>Luftfeuchtigkeit (%): ${details.relative_humidity}%</li>
        <li>Windrichtung (°): ${details.wind_from_direction}°</li>
        <li>Windgeschwindigkeit (km/h): ${Math.round(details.wind_speed * 3.6)} km/h</li>
    </ul>
`;
```

Wie schon beim Wetterstationen-Beispiel, können wir auch hier mit echten Datumsangaben arbeiten. Das Referenzdatum für die erste Vorhersage finden wir in `jsondata.properties.timeseries[0].time`. Wir initialisieren damit ein echtes Datum für den Zeitpunkt in einer Variablen `timestamp` und schreiben es über `.toLocaleString()` (<https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleString>) in der eingestellten Lokale des Browsers als Überschrift 4 vor die Liste

```javascript
let details = jsondata.properties.timeseries[0].data.instant.details;
let timestamp = new Date(jsondata.properties.timeseries[0].time);
let markup = `
    <h4>Wettervorhersage für ${timestamp.toLocaleString()}</h4>
    <ul>
        // Liste mit Werten
    </ul>
`;
```

**COMMIT**: <https://github.com/webmapping/forecast/commit/2d88f4daf45b338253c3bfeead11f9c07a532890>


### d) Wettersymbole für die nächsten 24 Stunden in 3 Stunden Abständen

Nachdem der `timeseries` Array aus 90 Einträge im Stunden-, bzw. 6-Stundenabstand besteht, können wir die Witterungsprognose für die nächsten 24 Stunden in 3 Stundenschritten implementieren. Wir verwenden dazu jeweils das Wettersymbol in `data.next_1_hours.summary.symbol_code` - die namentlich dazu passenden SVG-Icons liegen bereits im Unterverzeichnis `icons/`.

Dazu verwenden wir in eine **klassische for-Schleife** mit einer **Schleifenvariable** `i` die den `timeseries` Array abarbeitet. Nach jedem Schleifendurchlauf erhöhen wir die Zähler-Variable um `3` (Stunden) und beenden die Schleife beim Wert `24`.

```javascript
for (let i = 0; i <= 24; i += 3) {
}
```

Den Dateinamen des gewünschten Symbols finden wir im längsten verschachtelten Objekt, das wir bisher kennengelernt haben - in `jsondata.properties.timeseries[i].data.next_1_hours.summary.symbol_code` ;-)  Wir speichern ihn in einer Variablen `symbol`

```javascript
// Wettericons für die nächsten 24 Stunden in 3-Stunden Schritten
for (let i = 0; i <= 24; i += 3) {
    let symbol = jsondata.properties.timeseries[i].data.next_1_hours.summary.symbol_code;
}
```

Jetzt müssen wir nur bei jedem Schleifendurchlauf das Popup um ein **img-Element** mit dem gewünschten Symbol erweitern. Nachdem die SVG-Icons im Original recht groß sind, verkleinern wir sie mit einem `style-Attribut` auf `32px` Breite

```javascript
// Wettericons für die nächsten 24 Stunden in 3-Stunden Schritten
for (let i = 0; i <= 24; i += 3) {
    // SVG-Icon zum Markup hinzufügen
    let symbol = jsondata.properties.timeseries[i].data.next_1_hours.summary.symbol_code;
    markup += `<img src="icons/${symbol}.svg" alt="${symbol}" style="width:32px">`;
}
```

**COMMIT**: <https://github.com/webmapping/forecast/commit/524ae77c4c7b05e42aa08b30318c5563a18b2283>

Zur besseren Lesbarkeit ergänzen wir bei den Symbolen das jeweilige Datum als Tooltip über ein `title`-Attribut. Das Datum finden wir in `jsondata.properties.timeseries[i].time` - wir speichern es in einer Variablen `time` und formatieren es wieder mit `.toLocaleString()`in der eingestellten Lokale des Browsers

```javascript
// Wettericons für die nächsten 24 Stunden in 3-Stunden Schritten
for (let i = 0; i <= 24; i += 3) {
    let symbol = jsondata.properties.timeseries[i].data.next_1_hours.summary.symbol_code;
    let time = new Date(jsondata.properties.timeseries[i].time);
    markup += `<img src="icons/${symbol}.svg" alt="${symbol}" style="width:32px" title="${time.toLocaleString()}">`;
}
```

**COMMIT**: <https://github.com/webmapping/forecast/commit/4dda168ea32d69f49b750baa9be9c780724b271e>

### e) Ortsnamen der angeklickten Koordinate auflösen

Die Wettervorhersage ist jetzt für jeden Ort der Welt verfügbar, allerdings wissen wir nicht, welche Orte das sind, die wir anklicken. Es wäre schön, wenn wir die Koordinaten in Ortsangaben umwandeln könnten. Ein Dienst der OpenStreetMap macht das möglich: **Nominatim** - *Open-source geocoding with OpenStreetMap data* (<https://nominatim.org/>). Entwickelt als Debug-Tool für Objekte in der OpenStreetMap kann es nicht nur zum Suchen von Objekte über deren Namen, Adressen, etc. sondern auch zum **reverse geocoding** von lat/lng Koordinaten verwendet werden.

Am Beispiel der Koordinaten Innsbrucks können wir die API dieses Dienstes so aufrufen:

<https://nominatim.openstreetmap.org/reverse?lat=47.267222&lon=11.392778&zoom=12&format=jsonv2>

Als Ergebnis bekommen wir ein Objekt mit Key/Value Paaren zurück, dessen Attribut `display_name` eine brauchbare Ortsangabe für uns bereitstellt

Eine neue asynchrone Funktion `getPlaceName` wird diese API-Aufrufe für uns erledigen. Sie erwartet die URL des API-Aufrufs und liefert den Namen des ermittelten Orts zurück. Wir schreiben sie oberhalb von `showForecast`:

```javascript
// Ort über OpenStreetMap bestimmen
async function getPlaceName(url) {
    let response = await fetch(url);
    let jsondata = await response.json();
    return jsondata.display_name;
}
```

**COMMIT**: <https://github.com/webmapping/forecast/commit/cb3b4034629f968ec141245131c3d4a3e88631a1>

Aufgerufen wird `getPlaceName` ganz zu Beginn der Funktion `showForecast`, wo wir auch die URL des API-Aufrufs in einer Variablen `urlPlaceName` definieren. Das Ergebnis des Aufrufs speichern wir in der Variablen `placeName` und nachdem wir auf das Resultat möglicherweise länger warten müssen, verwenden wir `await` beim Aufruf.

```javascript
// Ort bestimmen
let osmUrl = `https://nominatim.openstreetmap.org/reverse?lat=${latlng.lat}&lon=${latlng.lng}&zoom=15&format=jsonv2`;
let placeName = await getPlaceName(osmUrl);
```

Damit können wir `placeName` beim Popup einbauen. Wir ergänzen bei dieser Gelegenheit auch noch die ungefähre Seehöhe des angeklickten Punkts, den uns `jsondata.geometry.coordinates[2]` zur Verfügung stellt. Als neuen HTML Tag setzen wir &lt;small> ein:

```javascript
let markup = `
    <h4>Wettervorhersage für ${timestamp.toLocaleString()}</h4>
    <small>Ort: ${placeName} (${jsondata.geometry.coordinates[2]}m)</small>
`;
```

**COMMIT**: <https://github.com/webmapping/forecast/commit/c2ad280cdc10c87bef87bec2e69c3cbe2eeb5b03>

### f) Download Links ergänzen

Zum Schluss ergänzen wir im Popup nach den Wettericons noch einen Download-Link zur JSON-Datei der Vorhersage und einen Link zu den Details des OSM-Datensatzes. Was macht noch einmal das `target`?

```javascript
// Links zu den JSON-Daten
markup += `
<p>
    <a href="${url}" target="met.no">Daten downloaden</a> |
    <a href="${osmUrl}" target="met.no">OSM Details zum Ort</a>
</p>
`;
```

**COMMIT**: <https://github.com/webmapping/forecast/commit/0f4c2982a45f6a462637eda31ac142981a5e557d>
