# Session 6: Vienna Sightseeing - Styling von Flächen, Linien, Punkten

`L.geoJSON` (<https://leafletjs.com/reference.html#geojson>) hat vier Optionen die es Wert sind, sich näher mit ihnen zu beschäftigen:

- `pointToLayer`: bestimmt das Aussehen von Markern

- `style`: ändert Aussehen von Flächen und Linien

- `onEachFeature`: erlaubt das Anhängen von Popups und Events

- `filter`: ermöglicht die Auswahl der GeoJSON-Objekte, die angezeigt werden sollen

Allen Optionen gemeinsam ist, dass sie nicht wie bis jetzt Werte, sondern Funktionen sind. Ausgeführt werden diese Funktionen, sobald eine GeoJSON-Geometrie zu einem Layer oder der Karte hinzugefügt wird.

## style-Option für Flächen und Linien

 Die Option `style` (<https://leafletjs.com/reference.html#geojson-style>) ändert das Aussehen von Flächen und Linien. Sie wird ausgeführt, wenn Leaflet aus einem GeoJSON Datensatz, eine Leaflet-Geometrie erzeugt. Beim Ausführen übergibt Leaflet den Datensatz der Geometrie mit ihren Attributen an die Funktion. Wir können sie in der Variablen `feature` abholen, wobei der Name `feature` nicht vorgegeben ist. Wir könnten die Variable auch anders benennen. Das Aussehen der Fläche oder Linie wird im Code-Block der Funktion über ein sogenanntes `Path options` Objekt definiert (<https://leafletjs.com/reference.html#path-option>). Über Key/Value Paare werden Eigenschaften wie Farben, Strichstärken, Transparenzen, etc. festgelegt und mit `return` zurückgegeben - erst damit ändert sich das Aussehen der Fläche oder Linie.

### Fußgängerzonen

Für die Fußgängerzonen setzen wir in der Funktion `loadZones` folgende Stile:

- die Standardfarbe `color` auf `#F012BE` (<https://leafletjs.com/reference.html#path-color>)

- die Strichstärke `weight` auf `1` (<https://leafletjs.com/reference.html#path-weight>)

- die Transparenz der Randlinie `opacity` auf `0.4` (<https://leafletjs.com/reference.html#path-opacity>)
        
- die Transparenz der Fläche `fillOpacity` auf `0.1` (<https://leafletjs.com/reference.html#path-fillopacity>)

```javascript
// Fußgängerzonen
L.geoJSON(jsondata, {
    style: function(feature) {
        return {
            color: "#F012BE",
            weight: 1,
            opacity: 0.4,
            fillOpacity: 0.1,
        }
    }
}).addTo(overlays.zones);
```

**COMMIT**: <https://github.com/webmapping/wien/commit/488acd2dc6d899a5c6f8e2e966f0c9c3e7135cd3> 

**Woher kommt die Farbe #F012BE?**: für unser Wien-Projekt werden wir Farben vom Projekt *COLORS - A nicer color palette for the web* (<https://clrs.cc/>) verwenden. Dort stehen ansprechende Standardfarben für uns bereit. Für die Fußgängerzonen wählen wir `FUCHSIA (#F012BE)`.

### Buslinien nach Linienfarbe

Auch Linien sind sehr einfach über eine GeoJSON `style` Option zu ändern.

Unsere Buslinien sind sogar nach Farben benannt, also liegt es nahe, sie auch in den passenden Farbe zu zeichnen. Über `console.log(feature.properties.LINE_NAME)` sehen wir die Namen der Linien: "*Red Line*", "*Yellow Line*", "*Blue Line*", "*Green Line*", "*Grey Line*" und "*Orange Line*"

Die Farbwerte für die Buslinien entnehmen wir wieder <https://clrs.cc/> - wir verwenden `RED`, `YELLOW`, `BLUE`, `GREEN`, `GRAY` und `ORANGE`

In einer `if-Abfrage` können wir für jedes Liniensegment den passenden Farbwert ermitteln. Wir speichern ihn in einer Variablen `lineColor` und geben ihn als `Path option` mit `return` zurück. Der Code für das Styling der Buslinien in `loadLines` sieht damit so aus:

```javascript
L.geoJSON(jsondata, {
    style: function (feature) {
        let lineColor;

        if (feature.properties.LINE_NAME == "Red Line") {
            lineColor = "#FF4136";
        } else if (feature.properties.LINE_NAME == "Yellow Line") {
            lineColor = "#FFDC00";
        } else if (feature.properties.LINE_NAME == "Blue Line") {
            lineColor = "#0074D9";
        } else if (feature.properties.LINE_NAME == "Green Line") {
            lineColor = "#2ECC40";
        } else if (feature.properties.LINE_NAME == "Grey Line") {
            lineColor = "#AAAAAA";
        } else if (feature.properties.LINE_NAME == "Orange Line") {
            lineColor = "#FF851B";
        } else {
            lineColor = "#111111";
        }

        return {
            color: lineColor
        };
    }
}).addTo(overlays.lines);
```
**COMMIT**: <https://github.com/webmapping/wien/commit/040f90cc601c8ef864d0a614acf7c97c4c5d171e>

### Einschub:  Map Icons Collection

Die **Map Icons Collection** unter <https://mapicons.mapsmarker.com/> bietet eine Vielzahl an Icons, die wir auch in eigenen Farbvarianten herunterladen können

- Sehenswürdigkeiten:

    - wir suchen nach *photo* und wählen das Photo-Icon

    - wir versuchen gleich das *customizable* Feature und wählen Weiß (`#FFFFFF `) als Farbe beim Farbwähler und klicken auf `Generate`

    - die Seite wird neu geladen, die Icons entsprechend angepasst und wir finden unsere Farbe in der URL <https://mapicons.mapsmarker.com/markers/media/photo/?custom_color=FFFFFF> wieder - dieses Feature werden wir später auch bei den Icons für die Bushaltestellen noch brauchen.

    - wir wählen das äußerst linke Icon, speichern es mit Rechtsklick im `icons/`-Verzeichnis als `photo.png` und fügen es zu GIT hinzu

    **COMMIT**: <https://github.com/webmapping/wien/commit/cd307633397cf12aca00da523303ff313d0d13d3>

- Bushaltestellen:

    - wir suchen nach *bus* und wählen das Bus-Icon

    - nachdem wir sechs Sightseeing Linien haben, benötigen wir auch sechs Busicons für die Haltestellen in den passenden Farben. Eine gute Verbindung zwischen Haltestellenfarbe und Icon-Name können wir über `feature.properties.LINIE_ID` herstellen, denn dort stehen Zahlen für die jeweilige Linienfarbe (z.B. `1` für "*Red Line*", `2` für "*Yellow Line*", usw.). Deshalb erzeugen wir sechs Icons mit passenden Farben von <https://clrs.cc/> und speichern sie als `bus_1.png` bis `bus_6.png`.

        - `bus_1.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=FF4136> (RED)
        - `bus_2.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=FFDC00> (YELLOW)
        - `bus_3.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=0074D9> (BLUE)
        - `bus_4.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=2ECC40> (GREEN)
        - `bus_5.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=AAAAAA> (GRAY)
        - `bus_6.png` - <https://mapicons.mapsmarker.com/markers/road-transportation/bus/?custom_color=FF851B> (ORANGE)

    - wir wählen wieder immer das äußerst linke Icon, speichern es mit Rechtsklick im `icons/`-Verzeichnis als `bus_1.png` bis `bus_6.png` und fügen alle Bus-Icons zu GIT hinzu

    **COMMIT**: <https://github.com/webmapping/wien/commit/625c1521e9a355fe4ed1251752ac61940e24db28>

- Hotels:

    - für Hotels und Unterkünfte suchen wir sechs Icons, die sich nach der Anzahl der Sterne der Unterkunft in `feature.properties.KATEGORIE_TXT` richten werden. Farblich werden alle gleich in <https://clrs.cc/> `PURPLE (#B10DC9)` gezeichnet

    - wir suchen *hotel*, wählen die Hotel-Icons `hotel_0stars` bis `hotel_5stars`, stellen die Farbe ein und speichern die Icons mit Rechtsklick als `hotel_0stars.png` bis `hotel_5stars.png` im `icons/` Verzeichnis. Der Einfachheit halber wählen wir immer die Mehrzahl `stars` - das erspart Fehler ;-)

    - der Link zu den generierten Icons ist damit <https://mapicons.mapsmarker.com/markers/restaurants-bars/hotels/hotel/?custom_color=b00dc9>

    **COMMIT**: <https://github.com/webmapping/wien/commit/0b8229d2e28c6d8cebce0c671acd15ed85dc3808>

Damit haben wir alle Icons vorbereitet und können sie bei den Sehenswürdigkeiten, Bushaltestellen und Hotels verwenden

## pointToLayer-Option für Icons

### Marker der Sehenswürdigkeiten durch ein Kamera-Icon ersetzen

Über die Option `pointToLayer` (<https://leafletjs.com/reference.html#geojson-pointtolayer>) können wir die Marker der Sehenswürdigkeiten sehr einfach mit Icons ersetzen. Sie ist wieder eine Funktion, der beim Aufruf das GeoJSON-Objekt und die Koordinate des Punkts übergeben wird - wir speichern beides in den Variablen `feature`  und `latlng`. Im Codeblock der Funktion erzeugen wir im einfachsten Fall einen Marker und geben ihn mit `return` zurück: 

```javascript
L.geoJSON(jsondata, {
    pointToLayer: function(feature, latlng) {
        return L.marker(latlng);
    }
}).addTo(overlays.hotels);
```

Über `L.icon` (<https://leafletjs.com/reference.html#icon>), eine Option des Marker-Objekts `L.marker` können wir den Standardmarker durch ein eigenes Icon ersetzen. Die Option `L.icon` besteht wieder aus Key/Value Paaren für die Eigenschaften des Icons. Über `iconUrl` können wir bestimmen, welches Bild als Icon verwendet werden soll. Wir geben den Pfad zum unserem Foto-Icon an:

```javascript
L.geoJSON(jsondata, {
    pointToLayer: function(feature, latlng) {
        return L.marker(latlng, {
            icon: L.icon({
                iconUrl: "icons/photo.png",
            })
        })
    }
}).addTo(overlays.hotels);
```

So weit so gut, leider müssen wir die Koordinate des Icons und den Popup Anker noch anpassen, denn per Default ist der Anfasspunkt von Icons Links Oben und passt weder zur echten geographischen Lage, noch zu jenem Punkt, an dem sich das Popup öffnet.

Wir müssen den Anfasspunkt unseres Icons deshalb über die Eigenschaften `iconAnchor` und `popupAnchor` verschieben. Beide Eigenschaften sind jeweils als Arrays definiert und legen fest, um wie viele Pixel der Anfasspunkt in x- und y-Richtung verschoben werden muss.

Wie kommen wir auf diese Werte? Die Spitze unseres Icons mit der Größe **32x37** Pixel liegt von Links, Oben gesehen bei **16** und **37** und diese Werte verwenden wir beim `iconAnchor` Array

Wenn wir auf das Popup klicken, verdeckt das Popup das Icon, deshalb schieben wir es beim `popupAnchor` Array um 37 Pixel nach Oben

```javascript
L.geoJSON(jsondata, {
    pointToLayer: function(feature, latlng) {
        return L.marker(latlng, {
            icon: L.icon({
                iconUrl: "icons/photo.png",
                iconAnchor: [16, 37],
                popupAnchor: [0, -37]
            })
        });
    }
}).addTo(overlays.hotels);
```

**COMMIT**: <https://github.com/webmapping/wien/commit/0d02b3e7114b97a2b24ba915c16a718a1ed90039>

Gut, dass alle Icons die selbe Größe haben, denn wir können damit für alle die selben Anfasspunkte verwenden. Das Einzige, was sich ändern wird, sind die Links zu den Icons im `icons/` Verzeichnis

### Marker der Hotels durch unterschiedliche Icons nach Sternen der Unterkunft ersetzen

Wie bei den Farben der Linien, verwenden wir eine **if-Abfrage** zur Bestimmung des Namens des jeweiligen Hotel-Icons und speichern den Icon-Namen in der Variablen `iconName`. Die Klassifizierung nach Sternen entnehmen wir dem Attribut `feature.properties.KATEGORIE_TXT` - dort steht "1*", "2*", "3*", "4*", "5*" oder "nicht kategorisiert". Der Code für die Hotels in der Funktion `loadHotels` sieht damit so aus:

```javascript
L.geoJSON(jsondata, {
    pointToLayer: function(feature, latlng) {
        let iconName;

        if (feature.properties.KATEGORIE_TXT == "1*") {
            iconName = "hotel_1stars.png";
        } else if (feature.properties.KATEGORIE_TXT == "2*") {
            iconName = "hotel_2stars.png";
        } else if (feature.properties.KATEGORIE_TXT == "3*") {
            iconName = "hotel_3stars.png";
        } else if (feature.properties.KATEGORIE_TXT == "4*") {
            iconName = "hotel_4stars.png";
        } else if (feature.properties.KATEGORIE_TXT == "5*") {
            iconName = "hotel_5stars.png";
        } else {
            iconName = "hotel_0stars.png";
        }

        return L.marker(latlng, {
            icon: L.icon({
                iconUrl: `icons/${iconName}`,
                iconAnchor: [16, 37],
                popupAnchor: [0, -37]
            })
        })
    },
}).addTo(overlays.hotels);
```

**COMMIT**: <https://github.com/webmapping/wien/commit/8ff527662a93b34325c418da5fbf9af75d877b8e>

### Marker der Haltestellen durch Bus-Icons nach Linienfarbe ersetzen

Wir erinnern uns, dass die ID und damit die Farbe der Linie im Attribut `feature.properties.LINIE_ID` verfügbar ist und wir bereits Bus-Icons in den richtigen Farben von `bus_1.png` bis `bus_6.png` erstellt haben.

Wir erinnern uns auch, dass wir Icons über das GeoJSON Options-Objekt `pointToLayer` erzeugen und die Anfasspunkte über `iconAnchor` und `popupAnchor` festlegen können

Der Code für Bus-Icons in Farben der Buslinien ist damit nur eine kleine Modifikation des Foto-Icons der Sehenswürdigkeiten 

Wir ersetzen über einen **Template string** mit *Backticks* die Zahl beim Bus-Icon durch das Attribut `feature.properties.LINE_ID` der jeweiligen Haltestelle

```javascript
L.geoJSON(jsondata, {
    pointToLayer: function(feature, latlng) {
        return L.marker(latlng, {
            icon: L.icon({
                iconUrl: `icons/bus_${feature.properties.LINE_ID}.png`,
                iconAnchor: [16, 37],
                popupAnchor: [0, -37]
            })
        })
    }
}).addTo(overlays.stops);
```

**COMMIT**: <https://github.com/webmapping/wien/commit/3ae5b78252189ab4c5b20b5f6ed2c8dff5250855>


## onEachFeature-Option für Popups

Für Popups können wir die Option `onEachFeature` (<https://leafletjs.com/reference.html#geojson-oneachfeature>) einsetzen. Sie ist wieder eine Funktion, die für jedes Element des GeoJSON-Objekts genau einmal ausgeführt wird. Beim Aufruf der Funktion werden der GeoJSON Datensatz und der erstellte Leaflet-Layer übergeben. Wir speichern beide Objekte in den Variablen `feature` und `layer`.

Das Popup definieren wir als **Template string** mit *Backticks*, verwenden die `feature.properties` Attribute `NAME`, `ADRESSE`, `THUMBNAIL` sowie `WEITERE_INF` und hängen es mit `bindPopup` an den Marker in der Variablen `layer`. Das Popup für die Sehenswürdigkeiten in `loadSights` sieht damit so aus:

```javascript
L.geoJSON(jsondata, {
    style: function (feature) {
        // style Code
    },
    onEachFeature: function (feature, layer) {
        layer.bindPopup(`
            <img src="${feature.properties.THUMBNAIL}" alt="*">
            <h4>${feature.properties.NAME}</h4>
            <address>${feature.properties.ADRESSE}</address>
            <a href="${feature.properties.WEITERE_INF}" target="wien">Webseite</a>
        `);
    }
}).addTo(overlays.sights);
```

**COMMIT**: <https://github.com/webmapping/wien/commit/882c6bb9bfcc8e7c4890173d3e6c4defb58e0649>

Drei Komponenten in diesem Popup sind neu:

- über `target="wien"` werden alle Links im selben Tab geöffnet. Beim ersten Aufruf eines Links erzeugt der Browser einen neuen Tab, merkt ihn sich als Tab "wien" und öffnet alle weiteren Links dort. Den Namen des Targets können wir frei vergeben.

- mit `alt="*"` signalisieren wir dem Browser, dass das Bild dekorativen Charakter hat. Screenreader werden es ignorieren.

- das neue Element &lt;address> (https://developer.mozilla.org/de/docs/Web/HTML/Element/address) bietet Platz für Kontaktinformationen, in unserem Fall die Adresse der Sehenswürdigkeit

## Plugins verwenden

### Plugin Leaflet.markercluster bei den Hotels einsetzen

Der Hotel-Layer besteht aus sehr vielen Icons und macht die Karte etwas unübersichtlich. Wir werden Icons deshalb zu Clustern zusammenfassen und bedienen uns dazu des Leaflet Plugins **Leaflet.markercluster**

Auf der Leaflet Plugin-Seite finden wir es unter "*Clustering/decluttering*" <https://leafletjs.com/plugins.html#clusteringdecluttering>

Die Plugin-Seite bei github.com ist hier zu finden: <https://github.com/Leaflet/Leaflet.markercluster>

Bei *Using the Plugin* verwenden wir wieder die *cdnjs* Option - wir steuern <https://cdnjs.com/libraries/leaflet.markercluster> an und verlinken `leaflet.markercluster.js`, `MarkerCluster.Default.css` und `MarkerCluster.css` in `index.html` 

```html
<!-- Leaflet Markercluster -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.5.3/leaflet.markercluster.js" integrity="sha512-OFs3W4DIZ5ZkrDhBFtsCP6JXtMEDGmhl0QPlmWYBJay40TT1n3gt2Xuw8Pf/iezgW9CdabjkNChRqozl/YADmg==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.5.3/MarkerCluster.Default.css" integrity="sha512-6ZCLMiYwTeli2rVh3XAPxy3YoR5fVxGdH/pz+KMCzRY2M65Emgkw00Yqmhh8qLGeYQ3LbVZGdmOX9KUjSKr0TA==" crossorigin="anonymous" referrerpolicy="no-referrer" />
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet.markercluster/1.5.3/MarkerCluster.css" integrity="sha512-mQ77VzAakzdpWdgfL/lM1ksNy89uFgibRQANsNneSTMD/bj0Y/8+94XMwYhnbzx8eki2hrbPpDm0vD0CiT2lcg==" crossorigin="anonymous" referrerpolicy="no-referrer" />
```

In `main.js` verwenden wir bei der Definition des Layers für die Hotels statt der `L.featureGroup` eine `L.markerClusterGroup`

```javascript
let overlays = {
    // alle anderen Layer
    hotels: L.markerClusterGroup().addTo(map),
}
```

**Ooops**: leider bekommen wir jetzt eine Fehler in der Konsole und unsere Karte ist verschwunden. Der Fehlermeldung entnehmen wir, dass offensichtlich eine Option `maxZoom`  beim Initialisieren der Karte in `L.map` fehlt. Das Plugin *Leaflet Markercluster* muss scheinbar wissen, wie weit maximal in die Karte gezoomt werden kann. Wir ergänzen `maxZoom` deshalb bei `L.map` und setzen seinen Zoom-Level auf 19.

```javascript
let map = L.map("map", {
    maxZoom: 19
}).setView([stephansdom.lat, stephansdom.lng], stephansdom.zoom);
```

Damit funktioniert das Plugin wieder und wir können in einem letzten Schritt über die Option `disableClusteringAtZoom` festlegen, dass ab Zoom-Level 17 alle Icons ohne Cluster gezeigt werden.

```javascript
let overlays = {
    // alle anderen Layer
    hotels: L.markerClusterGroup({
        disableClusteringAtZoom: 17
    }).addTo(map),
}
```

**COMMIT**: <https://github.com/webmapping/wien/commit/19adc5a5bf79895e112382c4d2fccfc8f942014a>

## Popups für die Sightseeing Linien, Haltestellen, Fußgängerzonen und Hotels

... das ist Teil der Workload (<https://webmapping.github.io/workload/wl6>) und wird später hier aufgelöst ;-)
