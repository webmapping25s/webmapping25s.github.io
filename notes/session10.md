# Session 10: Tips für die Projektphase, QGIS, Plugins, SCotty, IOM und TopoJSON

## 1. Tips für die Projektphase

- arbeitet nur im `ORGNAME.github.io` Repo

- legt **keine zusätzlichen Repos** an, sondern Unterverzeichnisse nach Thema und in den Unterverzeichnissen `index.html` Dateien und was ihr sonst noch benötigt

- am Beispiel eines **fiktiven Wind & Wetter Projekts** einer Organisation `windundwetter` könnte die Struktur so aussehen

    - die Organisation heißt `windundwetter`

    - das Repo muss `windundwetter.github.io` heißen - damit ist es automatisch über <https://windundwetter.github.io> online erreichbar ohne in des Settings noch etwas ändern zu müssen

    - die Struktur der Dateien und Verzeichnisse seht ihr hier: <https://github.com/windundwetter/windundwetter.github.io>

    - Startseite `index.html` mit einem zentralem Stylesheet `styles.css` und den .pdf Dateien für Konzept, Bericht und optionale Folien

        ```text
        https://windundwetter.github.io/index.html
        https://windundwetter.github.io/styles.css
        https://windundwetter.github.io/konzept.pdf
        https://windundwetter.github.io/bericht.pdf
        https://windundwetter.github.io/folien.pdf
        ```

    - Team-Seite in einem Unterverzeichnis mit einer `index.html` Seite und Bildern

        ```
        https://windundwetter.github.io/team/index.html
        https://windundwetter.github.io/team/images/klaus.jpg
        https://windundwetter.github.io/team/images/bernd.jpg
        ```

    - Kartenseite in einem Unterverzeichnis mit eigenem Skript, Style und Icon(s)

        ```text
        https://windundwetter.github.io/karte/index.html
        https://windundwetter.github.io/karte/main.js
        https://windundwetter.github.io/karte/main.css
        https://windundwetter.github.io/karte/icons/wind.png
        ```

- verlink wird dann
    
    - einfach mit dem Namen der HTML-Seite, oder `./` wenn beide Dateien im selben Verzeichnis sind: z.B. `index.html -> bericht.pdf`

        ```html
        <!-- in https://windundwetter.github.io/index.html -->
        <a href="konzept.pdf">Projekt-Konzept</a>
        <a href="./bericht.pdf">Projekt-Bericht</a>
        ```

    - mit dem Namen des Unterordners plus dem Namen der HTML-Seite, wenn die Zielseite in einem Unterordner liegt: z.B. `index.html -> team/index.html` (optional wieder mit `./`)

        ```html
        <!-- in https://windundwetter.github.io/index.html -->
        <a href="team/index.html">zur Teanseite</a>
        <a href="./team/index.html">zur Teanseite</a>
        ```

    - mit `../` wenn die Zielseite im übergeordneten Ordner liegt: z.B. `team/index.html -> index.html`

        ```html
        <!-- in https://windundwetter.github.io/team/index.html -->
        <a href="../index.html">zur Startseite</a>
        ```

    - mit `../SUBDIR/` wenn die Zielseite im parallelen Ordner liegt: z.B. `team/index.html -> karte/index.html`

        ```html
        <!-- in https://windundwetter.github.io/team/index.html -->
        <a href="../karte/index.html">zur Kartenseite</a>
        ```

    - mit `../` könnt ihr ein zentrale Stylesheet `styles.css` im Root-Verzeichnis in den Unterseiten einbinden

        ```html
        <!-- in https://windundwetter.github.io/team/index.html -->
        <link rel="stylesheet" href="../styles.css">

        <!-- oder in https://windundwetter.github.io/karte/index.html -->
        <link rel="stylesheet" href="../styles.css">
        <link rel="stylesheet" href="main.css"> <!-- wenn für die Karte noch eigene/andere Stile benötigt werden -->
        ```

- sprecht euch ab, wer, wo arbeitet

- immer **Git-Pull** bevor ihr zu Arbeiten beginnt

- immer **Commit + Git-Push** wenn man mit einem Bearbeitungsschritt fertig ist

- beachtet die Größe eurer Datenfiles - ab mehreren MBs wird's schwierig

    - vor allem originale GIS-Daten sind meist unbrauchbar, denn sie bestehen aus viel zu vielen Punkten pro Segment und teilweise aus viel zu vielen Attributen
        
    - generalisiert die Daten wenn nötig im GIS

    - reduziert die Nachkommastellen beim Export nach GeoJSON

    - entfernt unnötige Attribut-Spalten

    - kürzt zu lange Attribut-Namen

    - konvertiert GeoJSON Flächen/Liniengeometrien nach TopoJSON wenn nötig (siehe Unten)

    - wenn gar nichts hilft, verzichtet auf den Datensatz und sucht eine andere Lösung

    - achtet bei GPX Daten auf den Datenschutz! Verwendet keine Daten, wenn ihr euch nicht sicher seid, dass ihr das dürft. Fragt zur Sicherheit nach, ob ihr GPX-Daten von anderen Websites verwenden könnt

    - achtet auch bei Bildern und Videos auf die Rechte

    - verkleinert zu große Bilder. Wenn die Seitenbreite im CSS mit 1200 Pixel festgelegt ist, hat es keinen Sinn, Originalbilder mit 4000x 3000 Pixel unbearbeitet einzubauen. Das verlängert unnötig die Ladezeit der Projektseite. Verwendet GIMP um die Größen anzupassen.

**Task**: bevor wir weitermachen, erstellt ein neues Repo `tips` und klont es lokal

## 2. GeoJSON Dateien aus Shapefiles in QGIS - Mountainbike-Wegweisertafeln in Vorarlberg

### a) Daten downloaden

- <https://data.gv.at> ansteuern

- Suche: *Vorarlberg Mountainbike*

- **Mountainbike-Wegweisertafeln** in Vorarlberg <https://www.data.gv.at/katalog/de/dataset/mountainbike_tafeln>

- SHAPE-ZIP-Download in einem temporären Verzeichnis und auspacken

### b) Einlesen in QGIS

- QGIS starten

- "*Layer / Layer hinzufügen / Vektorlayer hinzufügen*"

- bei *Source Type* oben die Kodierung einstellen: `Latin1`, `ISO-8859-1`, nicht `UTF-8` - woher weiß ich das?
        
    - weil es bei den meisten GIS-Daten der Länder-GIS Abteilungen (leider) so ist?
        
    - weil es in `mountainbike_tafeln.cst` steht

    - weil es nach der Shapefile-Spezifikation (<https://en.wikipedia.org/wiki/Shapefile>) eigentlich in `mountainbike_tafeln.cpq` stehen müsste. Leider nimmt es ArcGIS (aus dem die meisten Daten der Länder stammen) nicht ganz so Ernst, deshalb ...

    - `mountainbike_tafeln.cst` in `mountainbike_tafeln.cpg` umbenennen ist auch eine Lösung, dann geht's auch mit der automatischen Erkennung

### c) Abspeichern als `.geojson`

- Rechte Maus auf "*Layer / Export / Objekte speichern als*"

    - GeoJSON

    - Dateiname: `tafeln.geojson` in einem neuem Unterverzeichnis `tips/mtb/`

    - KBS: EPSG:4326

    - optional *Select fields* anpassen - **Tipp** nehmt keine Spalten mit, die ihr nicht braucht

    - in jedem Fall *Persist layer metadata* checken, sonst gehen die properties verloren

    - COORDINATE_PRECISION: 5

    - RFC7946: YES  - was ist das? Das ist ein Verweise auf die GeoJSON Spezifikation <https://datatracker.ietf.org/doc/html/rfc7946>, kann also nicht schaden, wenn wir Standard konformes GeoJSON schreiben

    - WRITE_BBOX: YES - dann sehen wir die Koordinaten des Ausschnitts und können eventuell unseren Kartenausschnitt entsprechend setzen.

    - OK drücken und fertig

**COMMIT**: <https://github.com/webmapping25s/tips/commit/f975f7f5f00a3f17d5143d3240c6bf7674bfd0c9>

### d) Daten visualisieren

- Template <https://webmapping25s.github.io/templates/one_page_austria.html> verwenden und im Unterverzeichnis `tips/mtb/` als `index.html` speichern

- Titel und Quelle des Templates anpassen

- `tafeln.geojson` landen und anzeigen

    ```javascript
    // GeoJSON asynchron laden
    async function loadGeoJSON(url) {
        let response = await fetch(url);
        let geojson = await response.json();
        L.geoJSON(geojson).addTo(map);
    }
    loadGeoJSON("tafeln.geojson");
    ```

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/3d3a1b989d82b54ff558c2439ff3c10ddf6cd0f3>

- **NEU**: mit `.fitBounds()` <https://leafletjs.com/reference.html#map-fitbounds> und `.getBounds()` <https://leafletjs.com/reference.html#geojson-getbounds> können wir auf den tatsächlichen Ausschnitt der GeoJSON-Daten blicken. Damit wir den Extent der Marker bekommen, müssen wir den `L.geoJSON()` Aufruf nur in einer Variablen `let mtbLayer` speichern und dann dessen Ausdehnung mit `mtbLayer.getBounds()` abfragen. Gesetzt wird der Ausschnitt für die gesamte Karte, also beim Karten-Objekt mit `map.fitBounds()`.

    ```javascript
    // GeoJSON asynchron laden
    async function loadGeoJSON(url) {
        let response = await fetch(url);
        let geojson = await response.json();
        let mtbLayer = L.geoJSON(geojson).addTo(map);
        map.fitBounds(mtbLayer.getBounds());
    }
    loadGeoJSON("mountainbike.geojson");
    ```
    **COMMIT**: <https://github.com/webmapping25s/tips/commit/552c38529bdffaef447da7cdd45976e8e80dded4>

- die fertige Seite online: <https://webmapping25s.github.io/tips/mtb/>

## 3. Plugins, Plugins, Plugins & Scotty

### Leaflet.hash

<https://github.com/mlevans/leaflet-hash>

Zeigt Zoom-Level, Lat, Lng in der URL-Zeile des Browsers an. Wir können damit gut Bookmarks zu Ausschnitten erzeugen

Template: <https://webmapping25s.github.io/templates/one_page_europe.html> verwenden und im Unterverzeichnis `tips/plugins/` als `hash.html` speichern

Einbau:

- Script im &lt;head> Bereich der Seite einbauen - wir verwenden <https://jsdelivr.com>

    ```html
    <!-- Leaflet Hash -->
    <script src="https://cdn.jsdelivr.net/npm/leaflet-hash@0.2.1/leaflet-hash.min.js"></script>
    ```

- Plugin im Script initialisieren

    ```javascript
    // Leaflet Hash aktivieren
    new L.Hash(map);
    ```

**COMMIT**: <https://github.com/webmapping25s/tips/commit/aab05c9363eb68afc6acd9aab631efff13422aef>

- die fertige Seite online: <https://webmapping25s.github.io/tips/hash/>

### Leaflet.ResetView

<https://github.com/drustack/Leaflet.ResetView>

Link zur Startansicht der Seite bei der Zoom-control implementieren

Template: <https://webmapping25s.github.io/templates/one_page_tirol.html> verwenden und im Unterverzeichnis `tips/plugins/` als `resetview.html` speichern

Einbau:

- Script und Stylesheet im &lt;head> Bereich der Seite einbauen - wir verwenden <https://jsdelivr.com>

    ```html
    <!-- Leaflet Reset View -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@drustack/leaflet.resetview/dist/L.Control.ResetView.min.css">
    <script src="https://cdn.jsdelivr.net/npm/@drustack/leaflet.resetview/dist/L.Control.ResetView.min.js"></script>
    ```

- Plugin im Script initialisieren

    ```javascript
    // Reset View
    L.control.resetView({
        position: "topleft",
        title: "Startview anzeigen",
        latlng: map.getCenter(),
        zoom: map.getZoom(),
    }).addTo(map);
    ```

    Das Kartenzentrum und den Zoom-Faktor der Karte setzen wir als Optionen `latlng` und `zoom` über `map.getCenter()` und `map.getZoom()`

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/970dd5587a32dd7748f1ced464769809636afca3>

- die fertige Seite online: <https://webmapping25s.github.io/tips/resetview/>

### Leaflet.locatecontrol

<https://github.com/domoritz/leaflet-locatecontrol>

Ermöglicht, auf den eigenen Standort zu zoomen. Praktisch bei Karten, bei denen wir GeoLocation zur Navigation beim Benutzer, der Benutzerin benötigen

Template: <https://webmapping25s.github.io/templates/one_page_austria.html> verwenden und im Unterverzeichnis `tips/plugins/` als `locatecontrol.html` speichern

Einbau:

- Script und Stylesheet im &lt;head> Bereich der Seite einbauen - wir verwenden <https://jsdelivr.com>

    ```html
    <!-- Leaflet Control Locate -->
    <script src="https://cdn.jsdelivr.net/npm/leaflet.locatecontrol@0.84.2/dist/L.Control.Locate.min.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/leaflet.locatecontrol@0.84.2/dist/L.Control.Locate.min.css">
    ```

- Plugin im Script initialisieren

    ```javascript
    // Standort Control hinzufügen
    L.control.locate({
        strings: {
            title: "Eigenen Standort anzeigen",
            drawCircle: false,
        },
    }).addTo(map);
    ```

    Mit `drawCircle: false` kann man bei den Optionen den Kreis der Genauigkeit ausblenden

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/9bc8ef2153f88cf419be3d1d2b4e1fa5c35bafab>

- die fertige Seite online: <https://webmapping25s.github.io/tips/locatecontrol/>

### Leaflet.GeoSearch

<https://github.com/smeijer/leaflet-geosearch> - <https://smeijer.github.io/leaflet-geosearch/>

Geocodieren von Adressen mit Nominatim und anderen Services

Template: <https://webmapping25s.github.io/templates/one_page_tirol.html> verwenden und im Unterverzeichnis `tips/plugins/` als `geosearch.html` speichern

Einbau:

- Script und Stylesheet im &lt;head> Bereich der Seite einbauen - wir verwenden <https://jsdelivr.com>

    ```html
    <!-- Leaflet Geo Search -->
    <script src="https://cdn.jsdelivr.net/npm/leaflet-geosearch@4.2.0/dist/bundle.min.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/leaflet-geosearch@4.2.0/dist/geosearch.min.css">
    ```

- Plugin im Script initialisieren

    ```javascript
    // Geo Search
    const searchControl = new GeoSearch.GeoSearchControl({
        provider: new GeoSearch.OpenStreetMapProvider(),
        style: "bar",
        searchLabel: "Adresse suchen"
    });
    map.addControl(searchControl);
    ```

    Mit `style: "bar"` wird in der Karten Oben eine Suchleiste angezeigt. Alternativ kann über `style: "button"` eine Such-Icon bei unterhalb der Zoom-Control implementiert werden.

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/316e62819a976f2c91a1a49bcd86baa8951b3ad3>

- die fertige Seite online: <https://webmapping25s.github.io/tips/geosearch/>

### Scotty Widget Generator

Die ÖBB bieten ein Online Tool, mit dem man die Fahrplanabfrage direkt auf der eigenen Webseite einbinden kann, den: **Widget Generator**: <https://fahrplan.oebb.at/webapp/widgetgenerator.html>

- wir wählen Fahrplan, füllen das Ziel schon aus und wechseln bei den Suchoptionen auf Ankunftszeit

- nach einigen intuitiven Schritten bekommen wir als Ergebnis den Quellcode für ein &lt;script>-Element, das wir nach dem &lt;main> Bereich einbauen um sicher zu stellen, dass die Seite auch geladen ist, wenn das Skript auf das Widget zugreifen möchte

    ```html
    <main>
        <!-- Markup -->
    </main>

    <!-- Scotty Widget Generator -->
    <script src="https://fahrplan.oebb.at/webapp/staticfiles/hafas-widget-core.1.0.0.js?language=de_DE"></script>

    <!-- Hauptskript -->
    <script>
        // ...
    </script>
    ```

- den generierten &lt;div>-Code kopieren wir mit einer Überschrift unter den Karten-DIV und verkleinern bei dieser Gelegenheit auch die Höhe der Karte im CSS-Bereich auf `50vh` um Platz für das Widget zu schaffen

    ```html
    <div id="map"></div>

    <h3>Zufahrt mit öffentlichen Verkehrsmitteln</h3>
    <div data-hfs-widget="true" data-hfs-widget-cfg-colorscheme="light" data-hfs-widget-tp="true" data-hfs-widget-tp-postform="newtab" data-hfs-widget-tp-autosearch="true" data-hfs-widget-tp-arr="6020 Innsbruck, Innrain 52 Universitätshauptgebäude" data-hfs-widget-tp-enquiry="false"></div>
    ```

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/f1a23f156cf400c64db61adc16006ef00f045175>

- die fertige Seite online: <https://webmapping25s.github.io/tips/scotty/>

## 4. GeoJSON aus Tabellen erzeugen - IOM's Missing Migrants Project

<https://missingmigrants.iom.int/>

*Missing Migrants Project records since 2014 people who die in the process of migration towards an international destination, regardless of their legal status. As collecting information is challenging, all figures remain undercounts. The locations in most cases are approximate. Each number represents a person, as well as the family and community that they leave behind.*

Wir arbeiten in einem neuen Unterverzeichnis `tips/iom/` 

### a) Daten vorbereiten

Task: Daten im Download-Bereich von IOM als XLSX downloaden und nach GeoJSON konvertieren. Als Zwischenschritte verwenden wir **LiberOffice** zum Ergänzen der nötigen LAT/LNG Spalten und das Online Tool **Convert CSV to GeoJSON** (<https://www.convertcsv.com/csv-to-geojson.htm>) zur Umwandlung der XLSX-Daten in GeoJSON.

- Rohdaten downloaden & speichern

    - <https://missingmigrants.iom.int/> ansteuern

    - *Data / Download Data* (<https://missingmigrants.iom.int/downloads>) wählen

    - *XLS 2024* wählen, warten und mit LiberOffice öffnen

    - **Tipp**: das ganze Blatt markieren und die Breite der ersten Spalte ändern macht alle Spalten gleich breit und damit besser lesbar

- LAT/LNG Koordinaten mit LibreOffice ergänzen

    - wir finden sie in der Spalte *Coordinates*, getrennt durch ein Komma und ein Leerzeichen

    - wir splitten sie in LAT/LNG auf indem wir:

        - die *Coordinates* Spalte ans Ende der Tabelle kopieren um beim Trennen die nachfolgenden Spalten nicht zu überschreiben

        - die kopierte Spalte markieren und *Daten / Text in Spalten* auswählen

            - *Komma* und *Leerzeichen* als Trennoptionen wählen und auf *OK* klicken

            - einige Koordinaten werden nicht richtig getrennt - Handarbeit nötig und Achtung bei den Kommazeichen: "." statt "," verwenden!

        - die getrennten Spalten in *LAT* und *LNG* umbenennen
        
        - die unnötige leere Spalte löschen

- Online Tool *Convert CSV to GeoJSON* zum Konvertieren nach GeoJSON verwenden

    - <https://www.convertcsv.com/csv-to-geojson.htm> ansteuern

    - **Tipp**: die lästige Werbung kann in Firefox durch Installation des AddOns uBlock Origin eingedämmt werden

    - die gesamte Tabelle in *LibreOffice* markieren und kopieren

    - den Inhalt der Zwischenablage online bei *Step 1: Select your input* einfügen

    - das Ergebnis unter *Step 4: Generate output* speichern

    - `convertcsv.geojson` in `2024.geojson` umbenennen und nach `tips/iom/2024.geojson` verschieben

        **COMMIT**: <https://github.com/webmapping25s/tips/commit/2ae315e302e77207bbaf21e6e04265a4c9e74eaf>

### b) Daten visualisieren

Task: zur Visualisierung der Rohdaten verwenden wir ein vorbereitetes Template, laden die GeoJSON Daten asynchron und zeigen sie als Marker an. Jeder Marker erhält ein Popup mit ausgewählten Daten zum jeweiligen Ereignis.

- Template <https://webmapping25s.github.io/templates/one_page_europe.html> verwenden und im Unterverzeichnis `tips/iom/` als `index.html` speichern. Titel `Missing Migrants 2024` und Quelle anpassen

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/749276224a82dc1a0388176c7aed3b45bffbea09>

- im Script-Bereich am Ende des Skripts die GeoJSON-Daten über die asynchrone Funktion `loadGeoJSON(()` laden und visualisieren

    ```javascript
    // GeoJSON asynchron laden
    async function loadGeoJSON(url) {
        let response = await fetch(url);
        let geojson = await response.json();
        L.geoJSON(geojson).addTo(map);
    }
    loadGeoJSON("2024.geojson");
    ```

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/cadeeef0a16fd90e68b8a6350f5381d2189ba625>

- über `onEachFeature` und `.bindPopup()` zeigen wir für jeden Marker ein Popup mit Datum, Zahl der Vermissten oder Toten, Ort, Ursache sowie einen Link zu den Details bei <https://missingmigrants.iom.int> an, den wir immer in einem Tab `iom` öffnen

    ```javascript
    L.geoJSON(geojson, {
        onEachFeature: function(feature, layer) {
            layer.bindPopup(`
                <h4>${feature.properties["Incident Date"]}: ${feature.properties["Cause of Death"]}</h4>
                <p>${feature.properties["Total Number of Dead and Missing"]} dead or missing. ${feature.properties["Location of death"]}</p>
                <p><a href="https://missingmigrants.iom.int/migrant-incident-report?main_id=${feature.properties["Main ID"]}" target="iom">Weblink</a></p>
            `)
        }
    }).addTo(map);
    ```

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/2ad4dd71ff3bcb7d35277781024de8c8e71ffe43>

    **Was ist bei diesem Code speziell?**

    - immer wenn die Keys des Datenobjekts `feature.properties` Leerzeichen enthalten, müssen wir auf deren Werte über die sogenannte `Bracket notation` ([MDN Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Property_accessors#bracket_notation)) zugreifen - z.B.:

        ```javascript
        console.log(feature.properties["Incident Date"]);
        console.log(feature.properties["Total Number of Dead and Missing"]);
        ```

    - **Anmerkung**: wem das mit der *Bracket notation* zu kompliziert ist, kann natürlich vor der Konvertierung in LibreOffice die Header vereinfachen ...

    - das URL-Pattern für den Link auf Details bei `iom.int` findet sich bei den Seiten der Regionen (z.B. *Data / Mediterranean*) unter *Latest Incidents* in der *View* Spalte - siehe <https://missingmigrants.iom.int/region/mediterranean>. Der URL-Parameter `main_id` entspricht dabei unserem Attribut *Main ID* und kann für jeden Eintrag eingesetzt werden.

    - über `pointToLayer` können wir die Marker noch durch `L.circleMarker` (<https://leafletjs.com/reference.html#circlemarker-l-circlemarker>) ersetzen. Die Anzahl der betroffenen Personen in `feature.properties["Total Number of Dead and Missing"]` entspricht dabei der Kreisfläche. Den Radius `r` berechnen wir über die Formel `A = PI*r²` als `Quadratwurzel(A/PI)` und multiplizieren `A` noch mit einem frei wählbaren Skalierungsfaktor `scale` um die Kreise größer zu zeichnen. Beim Berechnen hilft uns das Javascript Math-Objekt mit `Math.sqrt()` und `Math.PI`
    
        ```javascript
        L.geoJSON(geojson, {
            pointToLayer: function(feature, latlng) {
                let scale = 30;
                let r = Math.sqrt(feature.properties["Total Number of Dead and Missing"]*scale/Math.PI);
                return L.circleMarker(latlng, {
                        radius: r
                });
            },
            onEachFeature: function(feature, layer) {
                // code
            }
        }).addTo(map);
        ```
 
    **COMMIT**: <https://github.com/webmapping25s/tips/commit/dd0b2bb33d82c9758274db6af22060ba3281ba84>

- die fertige Seite online: <https://webmapping25s.github.io/tips/iom/>

## 5. GeoJSON in TopoJSON konvertieren und visualisieren

Wenn GeoJSON-Dateien zu groß für die Verwendung im Projekt sind, kann man versuchen, sie in **TopoJSON** umzuwandeln. Die Spezifikation dieses Formats findet sich hier: <https://github.com/topojson/topojson-specification>

### a) GeoJSON Daten downloaden

- bei Google nach suchen "*landkreise deutschland geojson*"

- bei `OpenDataLab` werden wir fündig - <https://opendatalab.de/projects/geojson-utilities/>

    - Optionen *"Landkreise ...*" und "*simplify 200*" setzen
    
    - alle Landkreise auswählen 
    
    - die Landkreise temporär als `landkreise_simplify200.geojson` (~3MB) speichern

### b) GeoJSON nach TopoJSON konvertieren

Mit einem provisorischen Konvertierungstool am Server der Geographie, können wir  GeoJSON in TopoJSON umwandeln: <https://geographie.uibk.ac.at/data/topojson>

- das dauert ;-)

- und wird nach der Projektphase wieder offline genommen

- wir speichern das Resultat als `landkreise.topojson` in einem neuem Unterverzeichnis `tips/topojson/`

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/ac73b68ba849a5b28ea9e2642a280ecd8f802fb0>

- wer "Python 3 fit" ist kann sich dieses Tool auch selbst bauen:

    - das **topojson Modul** installieren - <https://pypi.org/project/topojson/>

    - dazu das **geojson Modul** installieren  - <https://pypi.org/project/geojson/>

    - dieses Skript konvertiert dann die GeoJSON-Daten in `landkreise_simplify200.geojson` nach TopoJSON als `landkreise.topojson`

        ```python
        import topojson
        import json

        # convert GeoJSON to TopoJSON
        with open("landkreise_simplify200.geojson", "r", encoding="UTF-8") as fh_in:
            data = json.load(fh_in)
            assert data["type"] == "FeatureCollection"
            topology = topojson.Topology(data)
            with open("landkreise.topojson", "w", encoding="UTF-8") as fh_out:
                fh_out.write(topology.toposimplify(0.0001).to_json())
                print("created ", fh_out.name)
        ```

- das Resultat `landkreise.topojson` hat nur noch `~800KB`. Wenn man vor der Konvertierung in QGIS alle Attribut-Spalten bis auf `AGS` (Kreisschlüssel) und `GEN` (Kreisname) löscht, kommt man immerhin auf `~650KB`.

### c) TopoJSON mit Leaflet.omnivore visualisieren

Als Template verwenden wir <https://webmapping25s.github.io/templates/one_page_europe.html> und speichern es im Unterverzeichnis `tips/topojson/` als `index.html`. Wir passen Titel und Quelle des Templates an "*TopoJSON mit Leaflet.omnivore*"

**COMMIT**: <https://github.com/webmapping25s/tips/commit/259ea0a53cf6326c9258a05adca7828a3383b6a8>

Danach können wir:

- das **Leaflet.omnivore Plugin** <https://github.com/mapbox/leaflet-omnivore> über jsdelivr.com in `index.html` referenzieren

    ```html
    <!-- Leaflet Omnivore -->
    <script src="https://cdn.jsdelivr.net/npm/leaflet-omnivore@0.3.4/leaflet-omnivore.min.js"></script>
    ```

- im Skript-Bereich die TopoJSON-Daten laden und anzeigen

    ```javascript
    // Landkreise Deutschland hinzufügen
    omnivore.topojson("landkreise.topojson").addTo(map);
    ```

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/329f48a950d2088b3c3895985dd5b2b402d465a5>

- damit wir mit den Daten, die *Leaflet.omnivore* geladen hat gleich wie mit GeoJSON arbeiten können (also `filter`, `onEachFeature`, etc. verwenden können) müssen wir noch eine Trick anwenden: wir definieren eine sogenannte "*Callback-Funktion*" als `L.geoJSON()` Aufruf - wir nennen sie `drawLandkreise` und referenzieren sie beim Laden der Daten mit *Leaflet.omnivore*. Das Plugin kümmert sich dann um den Rest und wir können in der "*Callback-Funktion*" wie von GeoJSON her gewohnt arbeiten. Der folgende Code definiert dieses Callback und implementiert ein Popup mit dem Namen der Landkreise:

    ```javascript
    let drawLandkreise = L.geoJson(null, {
        onEachFeature: function(feature, layer) {
            //console.log(feature)
            layer.bindPopup(`
                <h4>${feature.properties.GEN}</h4>
            `);
        }
    });
    // Landkreise Deutschland hinzufügen
    omnivore.topojson("landkreise.topojson", null, drawLandkreise).addTo(map);
    ```

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/a4eb395d91e03355c6de386b44dbd493dcf818b2>

- die asynchrone Funktion `loadGeoJSON` können wir löschen, denn *Leaflet.omnivore* übernimmt für uns das Laden der Daten

- bleibt noch, auf den Ausschnitt zu blicken - wir versuchen es wieder mit `.fitBounds()`  und `.getBounds()`

    ```javascript
    let topoLayer = omnivore.topojson("landkreise.topojson", null, drawLandkreise).addTo(map);
    map.fitBounds(topoLayer.getBounds);
    ```

- **HMMM** - scheinbar funktioniert das Ausschnitt setzen nicht, den wir bekommen in der Konsole den Fehler `Uncaught Error: Bounds are not valid`. Scheinbar sind die Landkreise zum Zeitpunkt unseres `.fitBounds()` Aufrufs noch nicht geladen - wir müssen also warten, bis alles gezeichnet ist und das können wir mit einem **Event-Listener* implementieren:

    ```javascript
    let topoLayer = omnivore.topojson("landkreise.topojson", null, drawLandkreise).addTo(map);
    topoLayer.on("ready", function() {
        map.fitBounds(topoLayer.getBounds())
    });
    ```

    **COMMIT**: <https://github.com/webmapping25s/tips/commit/6971bd1c1a607109d0906ee7c82b558774240b17>

- die fertige Seite online: <https://webmapping25s.github.io/tips/topojson/index.html>
