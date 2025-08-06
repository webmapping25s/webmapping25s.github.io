# Session 9: Bike Trail Tirol Beispiel

## Repo vorbereiten

- Repo `biketirol` erstellen, lokal klonen, Template auspacken, `add` und `push` wie gehabt

    **COMMIT**: <https://github.com/webmapping25s/biketirol/commit/0659ec9be6b90de244080471413a64e70ded77fb>

- todo-Marker auflösen: straight forward

  **COMMIT**: <https://github.com/webmapping25s/biketirol/commit/9ce210ce0470f8f1a5b9e10858d180f4b7979864>

- was ist neu im Template? **Gruppieren von Hintergrund-Layern** über `L.layerGroup()` <https://leafletjs.com/reference.html#layergroup>

    ```javascript
    let eGrundkarteTirol = {
        sommer: L.tileLayer("https://wmts.kartetirol.at/gdi_summer/{z}/{x}/{y}.png", {
            attribution: `Datenquelle: <a href="https://www.data.gv.at/katalog/dataset/land-tirol_elektronischekartetirol">eGrundkarte Tirol</a>`
        }),
        nomenklatur: L.tileLayer("https://wmts.kartetirol.at/gdi_nomenklatur/{z}/{x}/{y}.png", {
            attribution: `Datenquelle: <a href="https://www.data.gv.at/katalog/dataset/land-tirol_elektronischekartetirol">eGrundkarte Tirol</a>`,
            pane: "overlayPane",
        })
    };
    L.control.layers({
        "eGrundkarte Tirol Sommer": L.layerGroup([
            eGrundkarteTirol.sommer,
            eGrundkarteTirol.nomenklatur
        ]).addTo(map)
    }).addTo(map);
    ```

- im Verzeichnis `data/` liegen .gpx Dateien für alle Etappen

## Etappennavigation implementieren

Bevor wir die GPX-Dateien visualisieren, implementieren wir eine einfache Etappennavigation als Pulldown. Beim Wechseln der Etappe im Menü werden wir auch zur entsprechenden Online-Seite wechseln.

### a) LibreOffice Daten nach JSON konvertieren

Für die Implementierung des Pulldowns benötigen wir ein Javascript Etappenobjekt, das wir aus der LibreOffice Tabelle <https://webmapping25s.github.io/biketirol/etappen.ods> erstellen

Zur Umwandlung der Tabelle nach JSON verwenden wir **Online Convert CSV to JSON** <https://www.convertcsv.com/csv-to-json.htm>

- wir kopieren den Tabelleninhalt zu *"Step 1: Select your input"*

- in *"Step 5: Generate output"* sehen wir das Ergebnis - einen Array mit 21 Objekten: jede Tabellenzeile wird ein Objekt im Array, aus dem Header werden `keys`, aus  den Werten `values`

- wir erstellen eine neue Javascript-Datei <https://webmapping25s.github.io/biketirol/etappen.js> und kopieren das Ergebnis von CSV to JSON dorthin

- vor die erste eckige Klammer schreiben wir eine Variablendeklaration für eine Konstante `ETAPPEN`

    ```javascript
    const ETAPPEN = [
        {
            "user": "webmapping",
            "nr": 7,
            "titel": "Scharnitz - Achensee",
            // weitere Attribute
        },
        // mehr Etappen
    ];
    ```

- danach binden wir `etappen.js` in `index.html` ein

    ```javascript
    <script src="etappen.js"></script>
    ```

**COMMIT**: <https://github.com/webmapping25s/biketirol/commit/3e06316d9eaac51bd61c9582c711058c970ce4f6>

### b) das Pulldown mit einer for ... of Schleife und .innerHTML befüllen

Wir schreiben zuerst eine `for ... of` Schleife (<https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Statements/for...of>) für das `ETAPPEN`-Objekt. Bei jedem Schleifendurchgang ist das jeweilige Etappen-Objekt als Variable `etappe` verfügbar

```javascript
// Etappennavigation über Pulldownmenü
for (let etappe of ETAPPEN) {
    console.log(etappe);
    console.log(etappe.nr);
    console.log(etappe.user);
    console.log(etappe.titel);
}
```

**COMMIT**: <https://github.com/webmapping25s/biketirol/commit/be0e8fcf15027e69b295b0251b2d691fbd679eb5>

Dann ergänzen wir in `index.html` oberhalb des map-DIVs das Formularelement &lt;select>. Innerhalb dieses &lt;select>-Elements werden wir mit der *for-Schleife* &lt;option>-Elemente für alle Etappen erzeugen - so kann man sie sich vorstellen:

```html
<select id="pulldown">
    <option value="webmapping">Etappen 7: Scharnitz - Achensee</option>
</select>
```

Damit wir das Pulldown befüllen können, speichern wir in `main-js` mit `document.querySelector("#pulldown")` eine Referenz zum Pulldown und erzeugen den Markup der Option als Template-String:

```javascript
let pulldown = document.querySelector("#pulldown");
for (let etappe of ETAPPEN) {
    pulldown.innerHTML += `
        <option value="${etappe.user}">Etappe ${etappe.nr}: ${etappe.titel}</option>
    `;
}
```

**COMMIT**: <https://github.com/webmapping25s/biketirol/commit/8ff07f294c49f561c64a32d668fb609f8b4b3e92>


Bei jedem Schleifendurchgang wird dabei ein &lt;option>-Element zum `.innerHTML` des Pulldowns hinzugefügt. Als `value` verwenden wir den/die github Userin (`etappe.user`) der jeweiligen Etappe. Nummer (`etappe.nr`) und Titel der Etappe (`etappe.titel`) bilden den Label des Eintrag


Die eigene Etappe sollten wir vorselektieren - das geht über das Attribut `selected` bei &lt;option&gt;

Wir setzen es mit einer **if-Abfrage** bei der eigenen Etappe auf `"selected"`, bei allen anderen auf den leeren String `""`

```javascript
for (let etappe of ETAPPEN) {
    let selected = "";
    if ( etappe.nr == 7) {
        selected = "selected";
    }
    pulldown.innerHTML += `
        <option ${selected} value="${etappe.user}">Etappe ${etappe.nr}: ${etappe.titel}</option>
    `;
}
```

**COMMIT**: <https://github.com/webmapping25s/biketirol/commit/f3f21513a8768c5aa842ebfc230a79be72c5362e>



### c) bei Änderung im Pulldown zur Etappe wechseln

Immer wenn wir einen anderen Eintrag im Pulldown auswählen, wird ein **Event** ausgelöst - wir reagieren darauf mit einem **EventListener**

```javascript
pulldown.onchange = function(evt) {
    console.log(evt, evt.target);
}
```

In `evt.target` finden wir das Ziel der Änderung, also das Pulldown-Menü

In `evt.target.value` landet der aktuell selektierte Wert unseres `value`-Attributs, also der/die github Userin

Damit können wir den Browser anweisen, zur gewünschten Seite zu wechseln - wir müssen nur `window.location.href` neu setzen:

```javascript
// auf Wechsel in Pulldown reagieren
pulldown.onchange = function(evt) {
    window.location.href = `https://${evt.target.value}.github.io/biketirol`;
}
```

**COMMIT**: <https://github.com/webmapping25s/biketirol/commit/490767f47d54cbdb1d64252325a445b234ba9578>

Das Pulldown können wir natürlich auch mit CSS in `main.css` noch ein bißchen stylen

```css
#pulldown {
    font-size: 1.1em;
    margin-bottom: 0.3em;
}
```

**COMMIT**: <https://github.com/webmapping25s/biketirol/commit/b954b6bedba205c3940cab3f4118b13388faa5dd>

## GPX Daten visualisieren

GPX - das GPS Exchange Format kennt wohl jede(r), gängiges Format zum Aufzeichnen von Tracks mit GPS, am Handy, etc.- siehe <https://en.wikipedia.org/wiki/GPS_Exchange_Format> und Spezifikation <https://www.topografix.com/gpx.asp>

### a) Leaflet Plugin leaflet-elevation.js initialisieren

- <https://github.com/Raruto/leaflet-elevation>

- siehe"*How to use*" für den Einbau der Plugin-Dateien .js und .css in index.html

    ```html
    <!-- Leaflet Elevation -->
    <link rel="stylesheet" href="https://unpkg.com/@raruto/leaflet-elevation/dist/leaflet-elevation.css" />
    <script src="https://unpkg.com/@raruto/leaflet-elevation/dist/leaflet-elevation.js"></script>
    ```

    **COMMIT**: <https://github.com/webmapping25s/biketirol/commit/ee16ead9f013e5b7ad9d7a63a4c6c0a7a8c429db>

- Initialisieren des Plugins noch ohne Optionen in `main.js`

    ```javascript
    const controlElevation = L.control.elevation({
    }).addTo(map);
    controlElevation.load("data/etappe7.gpx");
    ```

- das Resultat in der Karte:

    - eine Profillinie mit Höhenlage in Farben

    - Start- und Endpunkt in Grün/Rot

    - Kilometrierung im Verlauf

    - Höhenanzeige `onmouseover`

    - Eingangs-View auf Profil gesetzt

- dazu ein Höhenprofil unterhalb der Karte

    - `onmouseover` wird Position/Höhe in Karte angezeigt

    - der Ausschnitt wird synchronisiert

    - Profilausschnitte `onmousedrag` auswählbar

    - Anzeige der Track-Statistik und Download-Möglichkeit

    - Höhenschieber rechts unten

    - ein-, ausklappbar

### b) Kosmetik über Optionen

- die Zeit in der Track-Statistik ausschalten - wir benötigen sie nicht

    ```javascript
    let controlElevation = L.control.elevation({
        time: false,
    }).addTo(map);
    ```

- den Profil DIV stylen

    - in `index.html` unter dem map-DIV einen neuen DIV mit der ID `profile`

    ```html
    <div id="profile"></div>
    ```

    - über die Optionen das Profil in diesen DIV schreiben und auf 300 Pixel Höhe setzen

    ```javascript
    let controlElevation = L.control.elevation({
        time: false,
        elevationDiv: "#profile",
        height: 300,
    }).addTo(map);
    ```

- den Profil DIV in `main.css` noch ähnlich wie die Karte stylen

    ```css
    #profile {
        margin-top: 0.3em;
        border: 1px solid gray;
    }
    ```

- Höhenprofil und Hintergrund über ein custom Theme stylen - der Name ist frei wählbar und wird als CSS-Klasse vom Plugin interpretiert

    ```javascript
    let controlElevation = L.control.elevation({
        // other options
        theme: bike-tirol
    }).addTo(map);
    ```

- Profil und Hintergrund über die Theme CSS-Klasse in main.css stylen

    - was ist **--ele-area** und **--ele-bg**? Das sind **CSS custom properties (variables)** <https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties>

    - beginnen mit `--` und können mit `var(--ele-area, Fallbackwert)` im Stylesheet verwendet werden

    - siehe <https://unpkg.com/@raruto/leaflet-elevation@2.4.0/dist/leaflet-elevation.css>

    ```css
    .bike-tirol {
        --ele-area: #3D9970;
        --ele-bg: white;
    }
    ```

**COMMIT**: <https://github.com/webmapping25s/biketirol/commit/5c1b77f96c51a87b41124beebead643868a75612>


## Leaflet Minimap Plugin für die Kartennavigation

- Plugin Leaflet.MiniMap: <https://github.com/Norkart/Leaflet-MiniMap>

- zuerst Script und Style in `index.html` einbauen

    ```html
        <!-- Leaflet Minimap -->
    <script src="https://cdn.jsdelivr.net/npm/leaflet-minimap@3.6.1/dist/Control.MiniMap.min.js" integrity="sha256-Hb/f1FMaLV+Qn6KSYbc92GxB2bcKp7/efxNeiduEo3g=" crossorigin="anonymous"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/leaflet-minimap@3.6.1/dist/Control.MiniMap.min.css" integrity="sha256-p0ab5p0FrFs9Tz4OZgifAvIP+Rd0VFUEbNYjVXVeh9w=" crossorigin="anonymous">
    ```

- dann das Plugin initialisieren, einen TileLayer für den Kartenhintergrund der Minimap bestimmen und über die Option `toggleDisplay` das Ein-, bzw. Ausklappen der Minimap ermöglichen

    ```javascript
    var gkTirol = new L.TileLayer("https://wmts.kartetirol.at/gdi_summer/{z}/{x}/{y}.png");
    var miniMap = new L.Control.MiniMap(gkTirol, {
        toggleDisplay: true
    }).addTo(map);
    ```

**COMMIT**: <https://github.com/webmapping25s/biketirol/commit/1cc61149ba73476182a8f747ea0476a10eaf0442>

## Leaflet Fullscreen plugin

Auf der Plugin-Seite von Leaflet finden wir das Leaflet Plugin **Leaflet.fullscreen** (<https://github.com/Leaflet/Leaflet.fullscreen>).

Wie bei jedem Plugin, müssen wir zuerst Scripts und Styles des Plugins im &lt;head>-Bereich von `index.html` hinzufügen - wir verwenden jsdelivr.com:

```html
<!-- Leaflet fullscreen -->
<script src="https://cdn.jsdelivr.net/npm/leaflet-fullscreen@1.0.2/dist/Leaflet.fullscreen.min.js" integrity="sha256-RQN1bmEq/28pzVG8Csu34O677VNj5/1xl1wUGyDjMuo=" crossorigin="anonymous"></script>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/leaflet-fullscreen@1.0.2/dist/leaflet.fullscreen.min.css">
```

Dann können wir das Plugin in `main.js` verwenden. Es gibt dabei zwei Möglichkeiten:

- als Option `fullscreenControl` bei `L.map`

    ```javascript
    // Fullscreen als Option von `L.map`
    L.map("map", {
        fullscreenControl: true
    });
    ```

- als `new L.Control.Fullscreen()`

    ```javascript
    // Fullscreen control hinzufügen
    map.addControl(new L.Control.Fullscreen());
    ```

**COMMIT**: <https://github.com/webmapping25s/biketirol/commit/72e9d4863ba1095ab27fa29bd94e21affc7d4382>

Damit ist unser fertiges Bike Trail Tirol Beispiel hier verfügbar: <https://webmapping25s.github.io/biketirol>
