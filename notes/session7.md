# Session 7: Wetterstationen Euregio Beispiel - Styling von Textlabeln

## Wetterstationen Euregio Beispiel vorbereiten

(Workload) Template <https://webmapping.github.io/templates/aws.zip> auspacken - neu ist eigentlich nur der WMTS-Layer von <https://lawinen.report> in `L.control.layers()`

**COMMIT**: <https://github.com/webmapping/aws/commit/17bcf326d470c4511da8b331ef77064e3976c374>

## Wetterstationen als Icons mit Popup visualisieren

### 1. Icons für die Wetterstationen

- (Workload) Wetterstationen als Icons mit `pointToLayer`, `L.marker` und `L.icon` in `L.geoJSON` hinzufügen

    ```javascript
    L.geoJSON(jsondata, {
        pointToLayer: function(feature, latlng) {
            return L.marker(latlng, {
                icon: L.icon({
                    iconUrl: "icons/wifi.png",
                    iconAnchor: [16, 37],
                    popupAnchor: [0, -37]
                })
            });
        },
    }).addTo(overlays.stations);
    ```

    **COMMIT**: <https://github.com/webmapping/aws/commit/028000d6cb3908112366793e480e762dab943060>

### 2. Popups für die Wetterstationen

- (Workload) Popup mit `onEachFeature` Option hinzufügen - Name (`feature.properties.name`) und Seehöhe (`feature.geometry.coordinates[2]`)

    ```javascript
    L.geoJSON(jsondata, {
        onEachFeature: function(feature, layer) {
            layer.bindPopup(`
                <h4>${feature.properties.name} (${feature.geometry.coordinates[2]}m)</h4>
            `);
        }
    }).addTo(overlays.stations);
    ```

    **COMMIT**: <https://github.com/webmapping/aws/commit/25b7700ef858a563904687fff4dd560def74abe2>

- Erweiterung des Popups um Liste mit möglicherweise "optionalen Werten" - nicht alle Werte werden überall gemessen. Wie können wir diese optionalen Werte durch z.B. "-" ersetzen? Es gibt zwei Möglichkeiten:

    - a) mit dem **logical OR || operator** (<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR>). Einfachste Variante, geht aber nur bei Werten die nicht auch "0" sein können, z.B. bei der Schneehöhe

        ```javascript
        <li>Schneehöhe (cm): ${feature.properties.HS || "-"}</li>
        ```

    - b) mit dem **Conditional (ternary) operator** (<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_operator>) mit **Bedingung / ? Fragezeichen / : Doppelpunkt**. Hier können wir auch mögliche "0" Werte bei der Termperatur berücksichtigen, indem wir abfragen, ob der Temperaturwert *undefined* ist:

        ```javascript
        layer.bindPopup(`
            <li>Lufttemperatur (°C): ${feature.properties.LT != undefined ? feature.properties.LT : "-"}</li>
        `);
        ```

        Ist so zu lesen: wenn die Bedingung "Lufttemperatur ist nicht `undefined`" zutrifft, dann schreibe die Lufttemperatur ins Popup, sonst ein Minuszeichen.

- das fertige Popup sieht dann so aus:

    ```javascript
    L.geoJSON(jsondata, {
        onEachFeature: function(feature, layer) {
            layer.bindPopup(`
                <h4>${feature.properties.name} (${feature.geometry.coordinates[2]}m)</h4>
                <ul>
                    <li>Lufttemperatur (C) ${feature.properties.LT !== undefined ? feature.properties.LT : "-"}</li>
                    <li>Relative Luftfeuchte (%) ${feature.properties.RH  || "-"}</li>
                    <li>Windgeschwindigkeit (km/h) ${feature.properties.WG || "-"}</li>
                    <li>Schneehöhe (cm) ${feature.properties.HS || "-"}</li>
                </ul>
            `);
        }
    }).addTo(overlays.stations);
    ```

    **COMMIT**: <https://github.com/webmapping/aws/commit/bb5eed3395d61eed5c0311533e54c5fe8d71c4d4>


- das Datum für den Messwert können wir auch noch hinzufügen und lesbarer formatieren

    - Datumsberechnungen in jeder Programmiersprache schwierig, weil so viele Formate und Zeitzonen.

    - Im Datensatz ist die Angabe der Zeit günstig, weil sie in einem standardisierten Format vorliegt:

        ```javascript
        feature.properties.date = "2025-05-08T11:30:00+02:00"
        ```

    - wird von der Javascript Methode `new Date()` erkannt (<https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Date>) und kann als *echtes* Datum verwendet werden - wir speichern das echte Datum in einer Variablen `pointInTime`.

    - mit `.toLocaleString()` kann das echte Datum nach der aktuell eingestellten Sprache im Browser formatiert werden

        ```javascript
        let pointInTime = new Date(feature.properties.date);
        layer.bindPopup(`
            <!-- Popup Code ... -->
            <span>${pointInTime.toLocaleString()}</span>
        `);
        ```

        **COMMIT**: <https://github.com/webmapping/aws/commit/0d01bd233872e916ba5d58a935a2f1f75841d8ff>

## Temperaturwerte als Text mit Hintergrundfarbe nach einer Skala darstellen

### 1. einen neuen Temperatur-Layer erstellen und in die Layercontrol einhängen

```javascript
let overlays = {
    stations: L.featureGroup(),
    temperature: L.featureGroup().addTo(map),
}

L.control.layers({
    // Hintergrundlayer
}, {
    "Wetterstationen": overlays.stations,
    "Temperatur": overlays.temperature,
}).addTo(map);
```

### 2. eine neue Funktion zum Zeichnen der Temperatur definieren

Diese Funktion verwendet die bereits geladenen GeoJSON-Daten und wird aus der asynchronen Funktion `loadStations` in der die Daten geladen werden aufgerufen

```javascript
function showTemperature(jsondata) {
    // Temperatur-Layer mit den GeoJSON-Daten zeichnen
}

async function loadStations(url) {
    let response = await fetch(url);
    let jsondata = await response.json();

    // Stations-Marker zeichnen

    // Temperatur-Layer zeichnen
    showTemperature(jsondata);
}
```

### 3. L.divIcon() für Marker mit Text verwenden

Den Code der Option `icon` ersetzen wir mit einem `L.divIcon()` (<https://leafletjs.com/reference.html#divicon>), dessen Optionen-Objekt aus zwei Key/Value Paaren besteht:

- die Option `className` (<https://leafletjs.com/reference.html#divicon-classname>) gibt jedem &lt;div> Element das wir als Icon erstellen ein Klassenattribut `class="aws-div-icon"` - wir können es dann zum Stylen der Texte verwenden

- die Option `html` (<https://leafletjs.com/reference.html#divicon-html>) bestimmt den HTML-Markup, der angezeigt werden soll. Wir setzen ihn als &lt;span> Element mit Template Syntax und Backticks auf die Lufttemperatur im Attribut `feature.properties.LT`

```javascript
icon: L.divIcon({
    className: "aws-div-icon",
    html: `<span>${feature.properties.LT}</span>
})
```

Die gesamte Funktion `showTemperature()` sieht damit so aus

```javascript
function showTemperature(jsondata) {
    L.geoJSON(jsondata, {
        pointToLayer: function(feature, latlng) {
            return L.marker(latlng, {
                icon: L.divIcon({
                    className: "aws-div-icon",
                    html: `<span>${feature.properties.LT}</span>`
                })
            })
        }
    }).addTo(overlays.temperature);
}
```

**COMMIT**: <https://github.com/webmapping/aws/commit/15650ad1be2289439ca68abc809aaef8af400b6d>

### 4. den Text Marker in main.css stylen

Über die Klasse `.aws-div-icon` können wir die Texte des &lt;span> Elements im Stylesheet `main.css` stylen:

```css
.aws-div-icon span {
    border: 1px solid silver;
    font-size: 1.25em;
    font-weight: bold;
    padding: 0.3em;
    text-shadow:
        -1px -1px 0 white,
        -1px 1px 0 white,
        1px -1px 0 white,
        1px 1px 0 white;
} 
```

**COMMIT**: <https://github.com/webmapping/aws/commit/2ec5f7a91b5b300f225ade07b35e85097735df0b>

Was passiert bei diesen CSS-Regeln?

- die ersten vier CSS Regeln sind selbsterklärend

- die `text-shadow` Anweisung ist für uns in dieser Form neu. Sie sorgt dafür, dass auf allen Seiten des Textes ein weißer Schatten entsteht, der den Text auch auf farbigem Hintergrund (wie wir ihn später implementieren werden) gut lesbar macht

- (mögliche Verbesserung, die in der Stunde nicht implementiert wurde) leider ist auch die Position des Icons noch nicht ganz richtig :-(

    - ... und wird es auch nicht hundertprozentig werden, denn die Positionierung von DIV-Icons ist nicht gerade einfach. Fügen wir vor dem `return` des DIV-Icons mit `L.marker(latlng).addTo(map)` einen zweiten Marker an der selben Position ein, erkennen wir, dass die Koordinate "ungefähr" links, oben beim Text liegt. Über einen Workaround im CSS von `main.css` verschieben wir deshalb die Icons nach links oben:

        ```css
        .aws-div-icon span {
            /* bestehende CSS Regeln */
            display: inline-block;
            transform: translate(-30%, -50%);
        }
        ```

    - das Verschieben der Marker bewirkt die CSS-Eigenschaft `transform` (<https://developer.mozilla.org/en-US/docs/Web/CSS/transform>), deren `translate` Anweisung zwei Offsets in `x` und `y` Richtung erwartet. Warum wir beim Verschieben `-30%` für die x-Richtung verwenden müssen bleibt allerdings ein Rätsel. Immerhin liegt der Textmarker jetzt mit seinem Zentrum an der annähernd richtigen Position.

### 5. GeoJSON Objekte beim Zeichnen filtern

Zwei Verbesserungen müssen wir in `main.js` noch implementieren. Vor allem die Stationen mit `undefined` bereiten uns Probleme, wir müssen sie daher beim Zeichnen ignorieren. Leaflet stellt uns dafür die GeoJSON Option `filter` (<https://leafletjs.com/reference.html#geojson-filter>) zur Verfügung

Genau wie `pointToLayer` ist `filter` eine Funktion, die für jedes Element in GeoJSON genau ein Mal ausgeführt wird. Liefert sie `true` zurück, wird der Marker gezeichnet, liefert sie `false` zurück, wird der Marker ignoriert. In der Funktion können wir auf die Attribute unseres Markers zugreifen und so entscheiden, ob wir `true` zurückgeben möchten. Wir schreiben die Option oberhalb von `pointToLayer` und liefern `true` zurück, wenn die Temperatur zwischen -50° und +50° liegt:

```javascript
L.geoJSON(jsondata, {
    filter: function (feature) {
        if (feature.properties.LT > -50 && feature.properties.LT < 50) {
            return true;
        }
    },
    pointToLayer: function(feature, latlng) {
        // code
    }
}).addTo(overlays.temperature)
```

Neu bei dieser *if-Abfrage* ist die Verknüpfung zweier Bedingungen mit dem **Logical AND (&&) operator** (<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND>)

**COMMIT**: <https://github.com/webmapping/aws/commit/70ed4b31542ad161b065789d0a738f164ff0d801>

Damit verschwinden Stationen ohne Temperatur und wir können die nächste Verbesserung beim Popup angehen - die Zahl der Nachkommastellen auf eine Nachkommastelle festlegen. Wir nützen dazu die bereits vom Neuseelandbeispiel bekannte Javascript Methode `.toFixed` (<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toFixed>):

```javascript
html: `<span style="background-color:${color}">${feature.properties.LT.toFixed(1)}</span>`
```

**COMMIT**: <https://github.com/webmapping/aws/commit/274961d948750083de640a281b5e59947f88e351>

Dieser Schritt hätte ohne das Ausschließen von Stationen ohne Temperatur unser Skript gestoppt, denn der Versuch den Wert `undefined` auf eine Nachkommastelle zu formatieren wäre natürlich gescheitert.

## Temperaturwerte in Farben umsetzen

### 1. Farb-Objekt COLORS mit Schwellen und Farben

Zur Umsetzung der Temperaturwerte in Farben der Rechtecke, definieren wir zunächst in einer neuen Javascript-Datei `colors.js` ein `COLORS`-Objekt  mit Farbwerten und dazugehörigen min/max Schwellen. Visual Studio Code hilft uns beim Erstellen im &lt;head> Bereich von `index.html` mit dem Baustein `script:src` und `STRG-Klick` auf den generierten Link. Den Kommentar dort erweitern wir entsprechend.

```html
<!-- eigene Stile, Farben und Hauptskript -->
<link rel="stylesheet" href="main.css">
<script src="colors.js"></script>
<script defer src="main.js"></script>
```

Die Farben mit Schwellen "leihen" wir uns von der **Temperaturkarte von Lawinen.report** (<https://lawinen.report/weather/map/temp>). Mit *Rechtsklick / Untersuchen* bei den farbigen Legenden-Kästchen können wir die CSS Farbwerte der einzelnen Kästchen ablesen. Wir notieren sie als `color` mit `min`/`max` Schwellen in unserem `COLORS`-Objekt das a) eine Konstante ist, b) aus Objekten je nach Layern und c) darin aus einem Array von Objekten für die Definition besteht. Und weil das auch bei der **Windkarte**(<https://lawinen.report/weather/map/wind>) funktioniert, definieren wir auch gleich noch Regeln für die Windgeschwindigkeit - so sieht unser COLORS-Objekt dann verkürzt aus:

```javascript
const COLORS = {
    temperature: [
        { min: -50, max: -25, color: "#9f80ff" },
        { min: -25, max: -20, color: "#784cff" },
        // und so weiter
    ],
    wind: [
        { min: 0, max: 5, color: "#ffff64" },
        { min: 5, max: 10, color: "#c8ff64" },
        // und so weiter
    ]
};
```

**COMMIT**: <https://github.com/webmapping/aws/commit/6209abfbe61fa17876c32cb31bfe1fe0658a2f65>

Das endgültige Resultat ist unter <https://webmapping.github.io/aws/colors.js> sichtbar ...

### 2. Funktion getColor(value, ramp) definieren

Nachdem wir den Task **eine Farbe für einen Wert nach Schwellen ermitteln** noch öfters brauchen werden, definieren wir eine Funktion `getColor`, die das für uns übernehmen wird. Sie erwartet beim Aufruf den Wert und die Farbpalette mit Schwellen, die in der Funktion dann als `value` und `ramp` verfügbar sind. In einer [for of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) Schleife werden alle Einträge in `ramp` mit dem `value` verglichen und sobald der Wert in die Schwellen passt, die entsprechende Farbe zurückgegeben. Wir schreiben die Funktion  noch vor der Funktion für das Zeichnen der Stationen und testen sie mit einem Aufruf, dem wir den Wert -40 und die Farbpalette COLORS.temperature übergeben.

Wie erwartet, bekommen wir den Farbwert `#9f80ff` angezeigt. Er steht für Temperaturen zwischen -50°  und -25°.

```javascript
// Farben nach Schwellen ermitteln
function getColor(value, ramp) {
    for (let rule of ramp) {
        if (value >= rule.min && value < rule.max) {
            return rule.color;
        }
    }
}
console.log(getColor(-40, COLORS.temperature));
```

### 3. Die ermittelte Farbe als style-Attribut beim &lt;span> Element setzen

Jetzt müssen wir nur noch in `pointToLayer` die richtige Farbe für das `L.divIcon` ermitteln, in der Variablen`color` speichern und als `style-Attribut` beim &lt;span> Element über die CSS Regel `background-color:${color}` einsetzen.

```javascript
L.geoJSON(jsondata, {
    filter: function(feature) {
        // code
    },
    pointToLayer: function(feature, latlng) {
        let color = getColor(feature.properties.LT, COLORS.temperature);
        return L.marker(latlng, {
            icon: L.divIcon({
                className: "aws-div-icon",
                html: `<span style="background-color:${color};">${feature.properties.LT.toFixed(1)}</span>`
            }),
        })
    }
}).addTo(overlays.temperature);
```

**COMMIT**: <https://github.com/webmapping/aws/commit/773d9c666d46f968e5417899c72b2fc1ddc4dbfc>

## Wind Layer implementieren

Nach dem selben Muster wie bei der Temperatur, können wir in einer neuen Funktion `showWind` den Wind-Layer implementieren

**COMMIT**: <https://github.com/webmapping/aws/commit/c1c8efeacd806eb32b555ca4ad8af25e0e646957>


## Layer Schneehöhen implementieren (Workload)

**COMMIT**: <https://github.com/webmapping/aws/commit/15bba9ed730985d481024f9b055f0844d94e772b>

## Layer Windrichtung & Windgeschwindigkeit implementieren (Workload)

**COMMIT**: <https://github.com/webmapping/aws/commit/68d069dbae836f2c9cde6bff4d03b76597138e6c>

## Rainviewer Plugin implementieren (Worklaod)

**COMMIT**: <https://github.com/webmapping/aws/commit/6217b3f4fb3a36cdfda5dbde33566b9c5803662b>

## Verbesserung 1: Lagerichtigkeit der L.divIcons

Leider ist die Position unserer bisher implementierten DIV-Icons nicht ganz richtig und wird es auch nicht hundertprozentig werden, denn die Positionierung von DIV-Icons ist recht schwierig.

Schalten wir den Stations-Layer ein erkennen wir, dass die Anfasspunkte der Textlabels und die Anfasspunkte der Icons nicht auf die selbe Koordinate zeigen. Die Texte sind nach Rechts Unten verschoben. Über einen Workaround in `main.css` können wir diesen Versatz ausgleichen und die Text nach Links oben verschieben:

```css
.aws-div-icon span {
    /* bestehende CSS Regeln */
    display: inline-block;
    transform: translate(-30%, -50%);
}
```

**COMMIT**: <https://github.com/webmapping/aws/commit/1fe5e08ead556f3790a8e0c1ca5edef4a9ae1d1f>

Mit der CSS-Eigenschaft `display: inline-block` (<https://developer.mozilla.org/en-US/docs/Web/CSS/display#inline-block>) wird der &lt;span> des Textlabels mit einer imaginären Box umgeben, die wir dann mit der CSS-Eigenschaft `transform:translate()` (<https://developer.mozilla.org/en-US/docs/Web/CSS/transform>) verschieben können. Die `translate` Anweisung definiert dabei zwei Offsets in `x` und `y` Richtung. Warum wir beim Verschieben `-30%` für die x-Richtung verwenden müssen bleibt allerdings ein Rätsel. Immerhin liegt der Textmarker jetzt mit seinem Zentrum an der annähernd richtigen Position.

## Zoom-Layer der Basis-Karte anpassen

Beim Zoom-In fällt auf, dass die Basiskarte bei höherer Zoom-Stufe verschwindet. Die Konsole ist dein Freund: 404 not found bei `tms/13/4353/2322.webp`. Ab Zoom-Stufe 13 ist die Hintergrundkarte vom LWD nicht mehr verfügbar (die brauchen die hohe Auflösung nicht).

```javascript

"Relief avalanche.report": L.tileLayer(
  "https://static.avalanche.report/tms/{z}/{x}/{y}.webp",
  {
    attribution: `© <a href="https://sonny.4lima.de">Sonny</a>, <a href="https://www.eea.europa.eu/en/datahub/datahubitem-view/d08852bc-7b5f-4835-a776-08362e2fbf4b">EU-DEM</a>, <a href="https://lawinen.report/">avalanche.report</a>, all licensed under <a href="https://creativecommons.org/licenses/by/4.0/">CC BY 4.0</a>`,
    maxZoom: 12,
  },
).addTo(map),
```

Mit `maxZoom` können wir einzelne Layer auf einen Zoom-Level beschränken. Beim Umschalten der Basis-Karte geht das Zoomen weiter hinein.

**COMMIT**: <https://github.com/webmapping/aws/commit/6fd03a7deb1c2b396137f8cf27fb44849ff425c0>

## Verbesserung 2: Windrichtung als Pfeile mit Windgeschwindigkeit als Farbe

Der kombinierte Layer für Windrichtung und Windgeschwindigkeit sieht noch besser aus, wenn statt des Texts für die Windrichtung, ein Pfeil in die entsprechende Richtung zeigt. Dazu verwenden wir als Text ein Font Awesome Pfeil-Icon (`fa-solid fa-circle-arrow-down`) und geben ihm die ermittelte Farbe. 

```javascript
L.divIcon({
    className: "aws-div-icon-wind",
    html: `<span><i style="color:${color}" class="fa-solid fa-circle-arrow-down"></i></span>`
})
```
Bevor wir die Pfeile rotieren, passen wir das CSS-Format `.aws-div-icon-wind span` noch etwas an: wir setzen die `font-size` auf `3em`, entfernen die `border` und verwenden einen schwarzen Schatten - das sieht besser aus

```css
.aws-div-icon-wind span {
    font-size: 3em;
    font-weight: bold;
    padding: 0.3em;
    padding: 0.3em;
    display: inline-block;
    transform: translate(-30%, -50%);
    text-shadow:
        -1px -1px 0 black,
        -1px 1px 0 black,
        1px -1px 0 black,
        1px 1px 0 black;
} 
```

Dann rotieren wir die Pfeile im `style`-Attribut des &lt;spans> mit `transform:rotate()` (<https://developer.mozilla.org/en-US/docs/Web/CSS/transform-function/rotate>) um den Wert im Attribut `feature.properties.WR`

```javascript
let color = getColor(feature.properties.WG, COLORS.wind);
return L.marker(latlng, {
    icon: L.divIcon({
        className: "aws-div-icon-wind",
        html: `<span><i style="transform:rotate(${feature.properties.WR}deg);color:${color}" class="fa-solid fa-circle-arrow-down"></i></span>`
    })
})
```

**COMMIT**: <https://github.com/webmapping/aws/commit/8ba13bd17651f1bfc5075835082cfe93b82cfa97>

**COMMIT**: <https://github.com/webmapping/aws/commit/39c8cf9749451554f9cb30ab95524ba99e6b1066>

Die Windgeschwindigkeit könn(t)en wir noch über `.bindTooltip` <https://leafletjs.com/reference.html#tooltip> als Tooltip beim Marker anzeigen

```javascript
let color = getColor(feature.properties.WG, COLORS.wind);
return L.marker(latlng, {
    icon: L.divIcon({
        className: "aws-div-icon-wind",
        html: `<span><i style="transform:rotate(${feature.properties.WR}deg);color:${color}" class="fa-solid fa-circle-arrow-down"></i></span>`
    })
}).bindTooltip(`${feature.properties.WG.toFixed(0)} km/h`)
```
