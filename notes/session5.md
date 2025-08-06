# Session 5: Vienna Sightseeing - GeoJSON, Layer & Leaflet Docs

## Neues Repo wien erstellen, clonen und online bringen

- wir loggen uns bei unserem github Account ein - <https://github.com/webmapping25s> 

- im Abschnitt *Repositories* erstellen wir mit *New* eine neues public repository `wien`

- bei "*&lt;> Code*" kopieren wir die Repo-Adresse <https://github.com/webmapping25s/wien.git> 

- weiter geht's in VS Code

    - mit *F1 / Git: Clone* clonen wir das Repo
    
    - im darauf folgenden Dialog wählen wir `D:\tmp` und klicken "*Select as Repository Location*"

    - VS Code hat jetzt einen `.git` Ordner im neuen Verzeichnis `D:\tmp\wien` abgelegt
    
    - mit "*Open*" öffnen wir das geklonte Repo und können dort weiterarbeiten

## Template auspacken, einchecken, pushen und online stellen

- wir haben ein Template für das Wien Beispiel vorbereitet: <https://webmapping25s.github.io/templates/wien.zip>

- Downloaden, auspacken und alles nach `D:\tmp\wien` verschieben

- mit dem Linkchecker Tool <https://webmapping25s.github.io/linkchecker.html> überprüfen, ob alles am richtigen Ort liegt

- alles zu GIT  hinzufügen und committen

    **COMMIT**: <https://github.com/webmapping25s/wien/commit/51ecc734ca7b342ed9373890a1b8c477cb28a115>

-  Push zurück auf <https://github.com/webmapping25s/wien>

- <https://github.com/webmapping25s/wien> neu laden - wunderbar

- **online verfügbar machen** über *Settings -> Pages -> Source* wie gehabt

- Online-Seite: <https://webmapping25s.github.io/wien>

## Wien Template erkunden

Das vorbereitete Template besteht aus `index.html`, `main.css` und `main.js` und beinhaltet (fast) nichts Neues. In `index.html` sind Font Awesome, die Leaflet Bibliothek, unser eigenes Stylesheet und Script eingebunden. Das Stylesheet `main.css` sieht etwas anders aus und bewirkt das neue Layout der Seite. In `main.js` finden wir den bereits bekannten Javascript-Code für eine Karte mit Marker und geöffnetem Popup. Nur der Hintergrundlayer ist anders und kommt von der frei verfügbaren **Verwaltungsgrundkarte von Österreich** (<https://basemap.at>)

```javascript
L.tileLayer('https://mapsneu.wien.gv.at/basemap/bmapgrau/normal/google3857/{z}/{y}/{x}.png', {
    maxZoom: 19,
    attribution: 'Datenquelle: <a href="https://www.basemap.at">basemap.at</a>'
}).addTo(map);
```

Ein Besuch bei der **Verwaltungsgrundkarte von Österreich** (<https://basemap.at>) zeigt, dass es dort noch mehr Hintergrundlayer gibt. Neben der von uns verwendeten Variant *Grau* gibt es noch *Standard*, *Overlay*, *HighDPI*, *Oberfläche* und *Gelände* als Raster-Layer. Wir werden später sehen, wie wir diese Layer einbauen können - für jetzt genügt uns die Variante *Grau*.

## Leaflet Dokumentation erkunden

Nach dem Javascript Crashkurs der letzten Session, widmen wir uns heute der Leaflet Bibliothek. Hilfe zur Verwendung von Leaflet finden wir auf der Leaflet Homepage (<https:leafletjs.com>) unter *Docs* (<https://leafletjs.com/reference.html>). Dort finden wir die **Leaflet API reference**, die uns zeigt, was wir mit der Leaflet Bibliothek alles umsetzen können. Der Begriff API steht dabei für *application programming interface* und bedeutet übersetzt *Programmierschnittstelle*.

Ein Blick in den Javascript Code von `main.js` zeigt uns drei Aufrufe der Leaflet Bibliothek in der Variablen `L`: `L.map`, `L.tileLayer` und `L.marker` - wir verwenden die Leaflet Dokumentation um uns diese Features näher anzusehen

### L.map - Karte initialisieren

Gleich unterhalb des Inhaltsverzeichnisses der Docs finden wir die Dokumentation für `L.map` - *The central class of the API — it is used to create a map on a page and manipulate it*. 

- über einen Klick auf das Link Symbol bei der Überschrift können wir, wie bei vielen anderen Einträgen der Dokumentation, einen direkten Link zur Dokumentation an dieser Stelle speichern - <https://leafletjs.com/reference.html#map>

- jeder Eintrag der Dokumentation besteht aus:

    - einem kurzen Beispiel (*Usage example*)

    - einer Anleitung, wie das Objekt erzeugt werden kann (*Creation*)

    - einer langen Liste an Optionen, die beim Erzeugen des Objekts als Javascript-Objekt (mit geschwungenen Klammern) angegeben werden können
    
    - *Events* die das Objekt in bestimmten Situationen aussendet, oder auf die es reagiert
    
    - *Methoden* und *Properties* die wir aufrufen, bzw. setzen können, um das Objekt oder dessen Verhalten zu ändern.

- Lösen wir die Zeile zum Initialisieren der Karte auf:

    ```javascript
    let map = L.map("map").setView([stephansdom.lat, stephansdom.lng], stephansdom.zoom);
    ```

    - `L.map`  wird hier erklärt: <https://leafletjs.com/reference.html#map-l-map>

    - `.setView()` finden wir im Abschnitt *Methods* des Map-Objekts hier: <https://leafletjs.com/reference.html#map-setview>

        - wir sehen, dass der Aufruf `.setView()` als **erstes Argument** die Lat/Long Koordinate des Zentrums erwartet. Folgen wir dem Link bei *LatLng* (<https://leafletjs.com/reference.html#latlng>), zeigt sich, dass es vier Möglichkeiten gibt, eine Koordinate in Leaflet zu definieren:

            ```javascript
            // als Array
            [stephansdom.lat, stephansdom.lng]

            // als Objekt
            {lng: stephansdom.lng, lat: stephansdom.lat}
            {lat: stephansdom.lat, lng: stephansdom.lng}

            // über eine Methode
            L.latLng(stephansdom.lat, stephansdom.lng)
            ```
        - zurück bei der Dokumentation von `setView()` sehen wir, dass als **zweites Argument** der Zoomfaktor als *Number* anzugeben ist - bei uns ist das `stephansdom.zoom`  mit dem Wert `12`

        - als **drittes Argument** könnten wir noch *Zoom/pan options* (<https://leafletjs.com/reference.html#zoom/pan-options>) als Javascript-Objekt angeben - müssen wir aber nicht, denn das Fragezeichen bei *options* signalisiert, dass es sich hier um ein optionales Argument handelt.

### L.tileLayer - Hintergrundkarte definieren

Mit `L.tileLayer` können wir eine Hintergrundkarte definieren. Versuchen wir unseren Eintrag aufzulösen:

```javascript
L.tileLayer('https://mapsneu.wien.gv.at/basemap/bmapgrau/normal/google3857/{z}/{y}/{x}.png', {
    maxZoom: 19,
    attribution: 'Hintergrundkarte: <a href="https://www.basemap.at">basemap.at</a>'
}).addTo(map);
```

- `L.tileLayer` wird hier erklärt: <https://leafletjs.com/reference.html#tilelayer>

- der Aufruf erwartet als **erstes Argument** ein *URL template*, über das die einzelnen Kartenkacheln gemäß des sogenannten **WMTS Standards** definiert werden

    ```javascript
    'https://mapsneu.wien.gv.at/basemap/bmapgrau/normal/google3857/{z}/{y}/{x}.png'
    ```

    Die drei Komponente in geschwungenen Klammern stehen für

    - `{z}`: Zoomfaktor

    - `{x}`: x-Index der Kachel

    - `{y}`: y-Index der Kachel

    Im (Firefox) Browser können wir uns mit `STRG+I` im Tab *Media*  die einzelnen Kartenkacheln aus denen sich die Karte zusammensetzt ansehen. Jede Kachel ist 256x256 Pixel groß und folgt dem URL Template pattern - z.B. <https://mapsneu.wien.gv.at/basemap/bmapgrau/normal/google3857/12/1420/2234.png>

- als **zweites Argument** können (optionale) Optionen als Javascript-Objekt angegeben werden

    - `maxZoom`: der maximale Zoomfaktor als *number* (<https://leafletjs.com/reference.html#tilelayer-maxzoom>)

    - `attribution`: die Kartenquelle als *string* - **HMMM**, in den Optionen können wir keinen Eintrag finden - warum? Optionen können auch von anderen Objekten **vererbt** werden und genau das trifft hier zu: `L.tileLayer` ist Teil eines übergeordneten Objekt `L.Layer` und übernimmt dessen `attribution` Option. In der Dokumentation müssen wir also auch die Einträge mit **Options inherited from ...** aufklappen und finden dort unsere Option `attribution` beim *Options inherited from Layer* (<https://leafletjs.com/reference.html#tilelayer-attribution>)


- bleibt noch das `.addTo(map)` im Anschluss an den `L.tileLayer()` Aufruf. Auch diese Methode finden wir bei den *Methods inherited from Layer* (<https://leafletjs.com/reference.html#tilelayer-addto>). Sie hängt den definierten Hintergrundlayer an die Karte, die wir mit `L.map` in der Variablen `map` definiert haben. Ohne `.addTo(map)` würden die Hintergrundkarte zwar definiert, aber nicht angezeigt werden.

### L.marker

```javascript
let marker = L.marker([stephansdom.lat, stephansdom.lng]).addTo(map);
marker.bindPopup(stephansdom.title).openPopup();
```

Der Code zum Erstellen des Markers ist ein gutes Beispiel für das sogenannte **Chaining** in Javascript, bei dem vereinfacht gesagt, eine Methode nach der anderen aufgerufen wird. Den Marker Code könnten wir ohne Variablendeklaration auch so schreiben:

```javascript
L.marker([stephansdom.lat, stephansdom.lng])
    .addTo(map);
    .bindPopup(stephansdom.title)
    .openPopup();
```

Übersichtlicher ist aber unsere Variante und in der Dokumentation sind die einzelnen Methoden hier zu finden:

- `L.marker` wird hier erklärt:  <https://leafletjs.com/reference.html#marker>

- `.addTo(map)` finden wir wieder bei *Methods inherited from Layer* (<https://leafletjs.com/reference.html#marker-addto>)

- `.bindPopup` finden wir bei *Popup methods inherited from Layer* (<https://leafletjs.com/reference.html#marker-bindpopup>). Die Dokumentation zeigt, dass wir den Inhalt des Popups als *<String|HTMLElement|Function|Popup>* angeben müssen. Unser Beispiel verwendet einen einfachen String. Optionen für das Popup sind wieder optional - wir könnten (und werden später) z.B. die maximale Breite `maxwidth` setzen ...

- `.openPopup` finden wir auch bei *Popup methods inherited from Layer* (<https://leafletjs.com/reference.html#marker-openpopup>). Auch die optionale Möglichkeit, das Popup an einer bestimmten Position der Karte zu öffnen, werden wir in einem späteren Beispiel einsetzen. Für jetzt öffnen wir Popups nur beim angeklickten Marker.

Damit sollten wir genügend Wissen über die Leaflet Dokumentation haben, um ein erstes neues Feature zu implementieren - fügt der Karte einen metrischen Maßstab hinzu

### L.scale - Kartenmaßstab

Dokumentation ist hier zu finden: <https://leafletjs.com/reference.html#control-scale>

```javascript
L.control.scale({
    imperial: false
}).addTo(map);
```

**COMMIT**: <https://github.com/webmapping25s/wien/commit/43e7df79d7a96c654901dcce6279286d3f843f0e>

Die kürzeste Variante bei den Optionen nützt den Umstand, dass per Default Einstellungen immer metrisch und imperial angezeigt werden. Es reicht damit, den englischen Maßstab auszuschalten. Ein Blick auf die Default-Werte lohnt sich immer!

### Ein Real World Beispiel: Sehenswürdigkeiten Wien

- wir steuern <https://data.gv.at>, das OpenData Portal von Österreich an

- über die Suche *Sehenswürdigkeiten* finden wir den Datensatz **Sehenswürdigkeiten Standorte Wien**

- auf der Katalog Seite der Sehenswürdigkeiten finden wir den Link zum GeoJSON bei **Downloads / WFS GetFeature (JSON) JSON**

- über **Herunterladen** öffnen wir <https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:SEHENSWUERDIGOGD&srsName=EPSG:4326&outputFormat=json>

- wir sehen uns die Datei `SEHENSWUERDIGOGD.json` in VS Code an und verwenden natürlich **F1 / Format Document** damit wir die Struktur besser erkennen

- wir erkennen, dass es sich um eine Sammlung von *Features* (`FeatureCollection`) handelt, wie viele es insgesamt sind und in welcher Projektion sie vorliegen erkennen wir ganz Unten bei `totalFeatures`

- die einzelnen *Features* sind im `features`-Array zu finden - dessen Einträge sind wiederum Javascript-Objekte

    ```json
    {
        "type": "Feature",
        "id": "SEHENSWUERDIGOGD.562346",
        "geometry": {
            "type": "Point",
            "coordinates": [
                16.36299031,
                48.21868751
            ]
        },
        "geometry_name": "SHAPE",
        "properties": {
            "OBJECTID": 562346,
            "NAME": "Sigmund Freud Museum",
            "ADRESSE": "Berggasse 19",
            "WEITERE_INF": "http://www.freud-museum.at",
            "THUMBNAIL": "https://www.wien.gv.at/viennagis/bubble/freudmuseum.jpg",
            "SE_ANNO_CAD_DATA": null
        }
    },
    ```

  - wir erkennen die Geometrie in `geometry` und die Attribute in `properties`

  - Leaflet versteht das GeoJSON-Format und kann es automatisch in Marker, Linien und Flächen umwandeln. Dazu müssen wir die Daten aber zuerst laden und das geht mit Hilfe einer Funktion, die noch dazu asynchron ausgeführt wird - wir müssen uns also zuerst noch kurz mit Funktionen beschäftigen

### Sidestep: Javascript Funktionen

Eine Funktion ist vereinfacht gesagt ein Codeblock mit einem Namen, der "irgend etwas tut" wenn wir ihn aufrufen. Beim Aufruf können wir Parameter für "das Tun" innerhalb der Funktion mitschicken und wenn der Codeblock abgearbeitet ist, kann die Funktion auch wieder einen einzelnen Wert, einen Array oder eine Objekt zurückliefern.

- eine einfach Funktion zum Addieren von zwei Zahlen sieht damit so aus:

    ```javascript
    function addiere(zahl1, zahl2) {
        return zahl1 + zahl2;
    }
    ```

- und so wird die Funktion `addiere` aufgerufen

    ```javascript
    let ergebnis = addiere(2, 4);
    console.log(ergebnis);
    ```

- so eine Funktion werden wir jetzt schreiben, um die GeoJSON-Daten der Sehenswürdigkeiten Wiens zu laden.

### GeoJSON Daten asynchron mit einer Funktion laden

- so sieht die Funktion aus:

    ```javascript
    // Sehenswürdigkeiten Standorte Wien
    async function loadSights(url) {
        let response = await fetch(url);
        let jsondata = await response.json();
        console.log(jsondata);
    }
    loadSights("https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:SEHENSWUERDIGOGD&srsName=EPSG:4326&outputFormat=json");
    ```

  - was passiert dabei?

    - in der ersten Zeile wird eine asynchrone (`async`) Funktion (`function`) mit dem Namen `loadSights` definiert

    - dieser Funktion wird in runden Klammern `()`  die Internetadresse des Datensatzes übergeben - wir speichern sie in einer Variablen `url` (den Namen können wir frei wählen)
    
    - der Codeblock der Funktion wird durch geschwungene Klammern `{}` abgegrenzt

    - danach wird auf die Antwort (`response`) des Servers gewartet (`await`) die er beim Aufruf (`fetch`) der Internetadresse (`url`) zurückliefert

    - ist die Antwort angekommen, kann im zweiten Schritt das GeoJSON-Objekt der Antwort (`response.json()`) in der Variablen `jsondata` gespeichert werden. Auch das kann dauern, deshalb warten wir wieder, bis es soweit ist (`await`)

    - **F12** zeigt uns schließlich in der Konsole das heruntergeladenen GeoJSON-Objekt

    - **Wichtig**: die Funktion wird natürlich nur ausgeführt, wenn wir sie mit `loadSights()` außerhalb der Funktion auch aufrufen! Beim Aufruf müssen wir auch die Internetadresse des Datensatzes übergeben - wir finden sie beim Download-Link der Open Data Seite der Sehenswürdigkeiten


- mit `L.geoJSON` (<https://leafletjs.com/reference.html#geojson>) können wir dieses GeoJSON-Objekt visualisieren. Dazu müssen wir nur `jsondata` als **erstes Argument** beim Aufruf übergeben. Mit `.addTo(map)` aus dem Abschnitt *Methods inherited from Layer* (<https://leafletjs.com/reference.html#geojson-addto>) hängen wir den Layer schließlich an die Karte. Das Ergebnis sind Marker für alle Sehenswürdigkeiten

    ```javascript
    async function loadSights(url) {
        let response = await fetch(url);
        let jsondata = await response.json();
        L.geoJSON(jsondata).addTo(map);
    }
    loadSights("https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:SEHENSWUERDIGOGD&srsName=EPSG:4326&outputFormat=json");
    ```

    **COMMIT** <https://github.com/webmapping25s/wien/commit/d8d9b4d0552f359dd121be170ad0f6a592ad6fc1>

- Als **zweites Argument** ergänzen wir die Datenquelle als Option `attribution` - wir finden diese Option bei *Methods inherited from Layer* (<https://leafletjs.com/reference.html#geojson-attribution>). Den gewünschten Text der dort stehen soll finden wir auf der Open Data Seite der Sehenswürdigkeiten unter *Lizenzzitat*

    ```javascript
    L.geoJSON(jsondata, {
        attribution: "Datenquelle: <a href='https://data.wien.gv.at'>Stadt Wien</a>"
    }).addTo(map);
    ```

    **COMMIT** <https://github.com/webmapping25s/wien/commit/8c9a8045dec8e00910eb3f6ed81f8bff4d36f0b1>

### Übungsaufgabe: wir erstellen drei weitere Layer

Über die Suche bei <https://www.data.gv.at/> suchen wir Layer mit JSON-Daten und implementieren sie mit den jeweiligen Vorgaben:

1. Funktion `loadLines`
    * Suche: *Sightseeing* - <https://www.data.gv.at/suche/?searchterm=sightseeing>
    * Datensatz *Touristische Kraftfahrlinien Liniennetz Vienna Sightseeing Linie Wien*
    * URL: <https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:TOURISTIKLINIEVSLOGD&srsName=EPSG:4326&outputFormat=json>
    * Overlay Key/Label: `lines` und "Liniennetz Vienna Sightseeing"

2. Funktion `loadStops`
    * Suche: *sightseeing* - <https://www.data.gv.at/suche/?searchterm=sightseeing>
    * Datensatz *Touristische Kraftfahrlinien Haltestellen Vienna Sightseeing Linie Standorte Wien*
    * URL: <https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:TOURISTIKHTSVSLOGD&srsName=EPSG:4326&outputFormat=json>
    * Overlay Key/Label: `stops` und "Haltestellen Vienna Sightseeing"

3. Funktion `loadZones`
    * Suche: *Fußgängerzonen* - <https://www.data.gv.at/suche/?searchterm=Fu%C3%9Fg%C3%A4ngerzonen>
    * Datensatz *Fußgängerzonen Wien*
    * URL: <https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:FUSSGEHERZONEOGD&srsName=EPSG:4326&outputFormat=json>
    * Overlay Key/Label: `zones` und "Fußgängerzonen"


**COMMIT**: <https://github.com/webmapping25s/wien/commit/e0f3fac7b0a8a8ee36722793f97c3efb0681fe26>

Unsere Karte ist jetzt voll mit Markern, Linien und Flächen und es wird Zeit, etwas aufzuräumen

### Code-Cosmetics bevor es weiter geht ...

- wir löschen den Marker für den Stephansdom

    **COMMIT**: <https://github.com/webmapping25s/wien/commit/8ccc496e8b31b697e7f0d959e15c71a832c1d2bc>

- wir verschieben alle Aufrufe unserer Funktionen ganz nach Unten zu einem Block. Das hilft uns beim Weiterentwickeln, denn wir können dann Funktionsaufrufe auskommentieren, die wir gerade nicht benötigen

    ```javascript
    // GeoJSON laden und visualisieren
    loadSights("https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:SEHENSWUERDIGOGD&srsName=EPSG:4326&outputFormat=json");
    loadLines("https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:TOURISTIKLINIEVSLOGD&srsName=EPSG:4326&outputFormat=json");
    loadStops("https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:TOURISTIKHTSVSLOGD&srsName=EPSG:4326&outputFormat=json");
    loadZones("https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:FUSSGEHERZONEOGD&srsName=EPSG:4326&outputFormat=json");
    ```

    **COMMIT**: <https://github.com/webmapping25s/wien/commit/cd73fcd4383fa7fc6c36792009b0e42b51018d35>


### L.control.layers für Hintergrundlayer

Mit `L.control.layers` (<https://leafletjs.com/reference.html#control-layers>) können wir Hintergrundlayer und Overlays für unsere Karte erzeugen. Das Prinzip der Implementierung ist recht einfach - Hintergrundlayer und Overlays werden nacheinander als Javascript Objekte definiert deren *Keys* die Label der Einträge sind und die *Values* den jeweiligen Hintergrundlayer, bzw. das jeweilige Overlay bestimmen.

- beginnen wir mit der einfacheren Übung und definieren wir eine *Layer control* mit zwei leeren Objekten für Baselayers und Overlays:

    ```javascript
    // Layer control
    L.control.layers({

    }, {

    }).addTo(map);
    ```

    **COMMIT**: <https://github.com/webmapping25s/wien/commit/8507dbffab2d3a5a1fbeee0f7cf7d5dad4b901da>

 - dann verschieben wir den bestehenden `L.tileLayer` Code für den Hintergrundlayer der basemap.at in das **ersten Argument** (bzw. Objekt) von `L.control.layers`:

    ```javascript
    // Layer control
    L.control.layers({
        "BasemapAT grau": L.tileLayer('https://mapsneu.wien.gv.at/basemap/bmapgrau/normal/google3857/{z}/{y}/{x}.png', {
            maxZoom: 19,
            attribution: 'Hintergrundkarte: <a href="https://www.basemap.at">basemap.at</a>'
        }).addTo(map)
    }, {

    }).addTo(map);
    ```

    **COMMIT**: <https://github.com/webmapping25s/wien/commit/86b553662fd203a03e7fd0f31ce83ce226140b34>

Im Code sehen wir zweimal `.addTo(map)`, warum? Das erste `addTo` bewirkt, dass der Layer *BasemapAT grau* per Default angezeigt wird, das zweite `addTo` hängt die *Layer control* an die Karte. Die fertige *Layer control* wird Rechts Oben angezeigt und ermöglicht uns, zwischen den einzelnen Hintergrundlayer über Radio buttons zu wechseln.

### L.control.layers für Overlays mit L.featureGroup

Overlays können als Objekt im **zweiten Argument** (bzw. Objekt) des Aufrufs von `L.control.layers` angegeben werden. Die *Keys* sind wieder Label für den Eintrag und die *Values* sind sogenannte **L.featureGroup** Objekte (siehe <https://leafletjs.com/reference.html#featuregroup>). Wir können sie uns als Container für die Geometrien vorstellen:

```javascript
// Layer control
L.control.layers({
    // Baselayer Code
}, {
    "Sehenswürdigkeiten": L.featureGroup().addTo(map),
    "Vienna Sightseeing Linien": L.featureGroup().addTo(map),
    "Vienna Sightseeing Haltestellen": L.featureGroup().addTo(map),
    "Fußgängerzonen": L.featureGroup().addTo(map),
}).addTo(map);
```

**COMMIT**: <https://github.com/webmapping25s/wien/commit/1a2f4af092d66eb4b449995308506f52c2448653>

Das `.addTo(map)` beim Overlays-Objekt kommt jetzt vier Mal, weil wir alle Layer per Default anzeigen wollen. Was noch nicht funktioniert, ist das Ein-, und Ausschalten der Layer. Wir können die Kontrollkästchen zwar verändern, am Karteninhalt ändert das allerdings nichts. Warum? Weil die GeoJSON Objekte noch nicht in den dafür vorgesehen `L.featureGroup` Layern landen. Damit wir das noch schaffen, müssen wir **vor der Layer control** ein weiteres Objekt hinzufügen, in dem wir die `L.featureGroup` Einträge definieren. Dann können wir sie nicht nur in der *Layer control* verwenden, sondern können beim Hinzufügen der GeoJSON-Daten in den Funktionen, die Geometrie mit `.addTo()` auch an die Layer hängen und nicht an die `map`.

Wir definieren deshalb vor der *Layer control* ein Objekt `overlays` mit vier Einträgen. Die *Keys* benennen wir ähnlich wie unsere Funktionen, die *Values* sind identisch mit den Einträgen in der *Layer control*

```javascript
// Overlays definieren
let overlays = {
    sights: L.featureGroup().addTo(map),
    lines: L.featureGroup().addTo(map),
    stops: L.featureGroup().addTo(map),
    zones: L.featureGroup().addTo(map),
}
```
**COMMIT**: <https://github.com/webmapping25s/wien/commit/2053406b5fa302c4a5bb36d3402c80a624eb5710>

Dann ersetzen wir die Einträge in der *Layer control* mit Verweisen auf unser `overlays` Objekt - aus `L.featureGroup().addTo(map)` bei den Sehenswürdigkeiten wird `overlays.sights` usw.

```javascript
// Layer control
L.control.layers({
    // Baselayer Code
}, {
    "Sehenswürdigkeiten": overlays.sights,
    "Vienna Sightseeing Linien": overlays.lines,
    "Vienna Sightseeing Haltestellen": overlays.stops,
    "Fußgängerzonen": overlays.zones,
}).addTo(map);
```

**COMMIT**: <https://github.com/webmapping25s/wien/commit/c9015544b93edc4671bbcb55e67f8004ede5eb75>

Damit sind wir fast fertig, denn jetzt müssen wir die GeoJSON-Daten auch noch in die Overlays schreiben. In jeder der vier Funktionen ändern wir den `.addTo(map)` Aufruf zu `.addTo(overlays.sights)` usw.

```javascript
L.geoJSON(jsondata, {
    attribution: "Datenquelle: <a href='https://data.wien.gv.at'>Stadt Wien</a>"
}).addTo(overlays.sights);
```

**COMMIT**: <https://github.com/webmapping25s/wien/commit/730e9c177194d9da01edde97317b09273089482e>

### Leaflet.providers Plugin für Hintergrundkarten

Der Code für unseren Baselayer mit der basemap.at ist recht kompliziert und die Frage ist: geht das nicht einfacher? Die Antwort: Ja, mit einem Leaflet Plugin, das uns viele Hintergrundlayer zur Verfügung stellen kann - es heißt **Leaflet.providers**.

Auf der Leaflet Homepage finden wir es bei *Plugins* und dort bei *Basemap providers* (<https://leafletjs.com/plugins.html#basemap-providers>) / *leaflet-providers*. Das Demo (<https://leaflet-extras.github.io/leaflet-providers/preview/>) zeigt alle verfügbaren Layer, unter anderem auch die Layer der Verwaltungsgrundkarte Österreichs.

Hilfe zur Installation des Plugins finden wir beim Link *leaflet-providers* (<https://github.com/leaflet-extras/leaflet-providers>) unter dem Abschnitt *CDN*. Fügen wir deshalb in der `index.html` Datei unter dem Leaflet Skript, das *Leaflet providers* Skript hinzu:

```html
<!-- Leaflet providers -->
<script src="https://unpkg.com/leaflet-providers@latest/leaflet-providers.js"></script>
```

Dann müssen wir nur noch den TileLayer Code in `main.js` mit einem Aufruf der *Leaflet providers* Bibliothek ersetzen - das *Usage* Beispiel zeigt wie. Wir kopieren den Code unterhalb des TileLayers und bekommen eine neue "Wasserfarben" Hintergrundkarte. Ändern wir das *Leaflet providers Keyword* `Stadia.StamenWatercolor` in `BasemapAT.grau`, sieht die Karte wieder aus wie zuvor.

```javascript
L.tileLayer.provider('BasemapAT.grau').addTo(map);
```
Wie bereits erwähnt, gibt es die Verwaltungsgrundkarte Österreichs in mehreren Varianten, deshalb können wir im nächsten Schritt alle Varianten mit Hilfe des *Leaflet providers* Plugins als **Layer control** verfügbar machen.

```javascript
// Layer control
L.control.layers({
    "BasemapAT": L.tileLayer.provider('BasemapAT.basemap'),
    "BasemapAT grau": L.tileLayer.provider('BasemapAT.grau').addTo(map),
    "BasemapAT HighDPI": L.tileLayer.provider('BasemapAT.highdpi'),
    "BasemapAT Orthofoto": L.tileLayer.provider('BasemapAT.orthofoto'),
    "BasemapAT Overlay": L.tileLayer.provider('BasemapAT.overlay'),
    "BasemapAT Relief": L.tileLayer.provider('BasemapAT.terrain'),
    "BasemapAT Oberfläche": L.tileLayer.provider('BasemapAT.surface'),
}).addTo(map);
```
**COMMIT**: <https://github.com/webmapping25s/wien/commit/9623821f0c52095b96d1bda0b7c90c89c10448c6>


## Workload

4. Layer: Funktion `loadHotels`
    * Suche: *Hotels*
    * Datensatz *Hotels und Unterkünfte Standorte Wien*
    * URL: <https://data.wien.gv.at/daten/geo?service=WFS&request=GetFeature&version=1.1.0&typeName=ogdwien:UNTERKUNFTOGD&srsName=EPSG:4326&outputFormat=json>
    * Overlay Key/Label: `hotels` und "Hotels und Unterkünfte"

    **COMMIT**: <#>

