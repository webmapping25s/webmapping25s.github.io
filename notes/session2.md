# Session 2 - HTML Struktur, GIMP & First Leaflet map (13.3.2024)

## Kurz zu den Tutorials

- neue Elemente: &lt;br> für "break" (=weicher Zeilenumbruch), &lt;ol> für "ordered list" und &lt;video>

- neues id-Attribut kann bei jedem Element verwendet werden, muss eindeutig sein und kommt später ...

- Kommentare mit &lt;!-- comment -->

- wenn man explizit Kleiner-als-Zeichen oder Größer-als-Zeichen will, dann `&lt;` und `&gt;`  verwenden

- `target="_blank"` bei Links ist ein zweischneidiges Schwert: stelle dir vor, jeder Link im Internet geht in einem neuen Fenster auf ;-) Wir einigen uns darauf, dass wir es vorerst nicht verwenden

## Überleitung Seitenstruktur

- &lt;span>, &lt;div> im Tutorial sind neu

- es gibt noch mehr Element zur Strukturierung der Seite, z.B. &lt;header>, &lt;main>, &lt;footer>, &lt;article> und die werden wir jetzt verwenden

- doch zuvor: *F1  Format Document* verwenden

    **Achtung:** wenn beim Formatieren auch der Fließtext der Absätze umgebrochen wird, rückgängig machen, denn das sieht nicht gut aus
        
    die VS Code Settings können das verhindern:

    - File / Preferences / Settings
    - nach "wrap" suchen
    - Extensions / HTML anwählen
    - Html Format Wrap Line Length auf "0" stellen
    - danach noch einmal *F1  Format Document*

    **COMMIT:** <https://github.com/webmapping/nz/commit/7e4f7d0151d59f7bc85aabd2fbbb978a1e438315>


## Hauptstruktur für die Etappenseite

Wir geben der Seite Struktur und verpacken alles im &lt;body> in ein &lt;main>-Element

```html
<body>
    <main>
        <!-- unser bisheriger Content -->
    </main>
</body>
```

- **Tipp**: mit "*F1 / Wrap with Abbreviation*" können ganze Blöcke auf einmal in ein Element geschoben werden - Vorgang dabei:

    - den gewünschten Block markieren

    - "*F1 / Wrap with Abbreviation*" wählen

    - den Namen des gewünschten Elements schreiben (z.B. *main*)

    - ENTER drücken

    **COMMIT:** <https://github.com/webmapping/nz/commit/6b9dbdcdeacb4e638e60a4131f7f927ca3d3f31b>

## Navigation verschieben

- den &lt;nav>-Block ganz Unten schieben wir in ein &lt;footer>-Element

    ```html
    <body>
        <main>
            <! ... -->
        </main>
        <footer>
            <nav>
                <! ... -->
            </nav>
        </footer>
    </body>
    ```

    - wieder "*F1 / Format Document*" verwenden

        **COMMIT:** <https://github.com/webmapping/nz/commit/b0dd14bf7c2b429e7d0d0b3657db4043cad56b23>

## Header erstellen

- oberhalb von &lt;main> erstellen wir ein &lt;header>-Element - es wird zwei Bilder beinhalten

    ```html
    <body>
        <header>
        </header>
        <main>
            <! ... -->
        </main>
        <footer>
            <nav>
                <! ... -->
            </nav>
        </footer>
    </body>
    ```

    **COMMIT:** <https://github.com/webmapping/nz/commit/91ef1d8ea7d490a7b7b98f16748caef56263dfdd>
 
- "*F1 / Format Document*" kann nicht schaden

    **COMMIT:** <https://github.com/webmapping/nz/commit/7e4f7d0151d59f7bc85aabd2fbbb978a1e438315>

### Bannerbild 1280x365px

- bei Pixabay nach "*Tongariro*" suchen - <https://pixabay.com/de/photos/tongariro-vulkan-herr-der-ringe-583179/>

    - Version mit 1920 Pixel Breite downloaden

    - landet bei uns im Download-Ordner ...

- Bild zuschneiden mit <https://gimp.org/>

    - Breite auf 1280 Pixel bringen

        - Bild → Bild skalieren → 1280x832 Pixel einstellen

        - TIPP: in den Eingabefeldern könnte man auch rechnen ...

    - Bild Oben und Unten abschneiden

        - Bild → Leinwandgröße  → 1280x365 Pixel einstellen

        - Ebene → Ebene auf Bildgröße

        - Datei → Exportieren nach → `images/header.jpg` → Exportieren

            - Qualität: 90
            - Vorschau speichern wegklicken
            - EXIF, XMP, IPTC wegklicken

    - Bild erscheint im images-Verzeichnis von VS Code

        **COMMIT:** <https://github.com/webmapping/nz/commit/be32bc7f3807e6382e48ec777ff05ccef039c7c8>

### User-Bild 100x100px

- bei Pixabay nach "*Bus*" suchen - bringt ein Autospielzeug - <https://pixabay.com/de/photos/auto-spielzeugauto-bus-vw-fahrzeug-2583303/>

    - Vorschaubild speichern unter `images/user.jpg`

    - Bild wird im images-Verzeichnis von VS Code angezeigt

- Größe ändern auf 100x100 mit <https://gimp.org/>

    - Doppelklick auf Symbol "rechteckige Auswahl" oder Werkzeuge → Auswahlwerkzeuge → Rechteckige Auswahl (R)

        - Seitenverhältnis → Fixiert → 1:1

        - Rahmen aufziehen und mit Drag positionieren

        - Position und Größe werden angezeigt, auch der Bildmittelpunkt

        - Größe kann auch geändert werden an den Ecken

        - wenn alles passt ...

    - Bild → Auf Auswahl zuschneiden

    - Bild → Bild skalieren → 100x100 → Skalieren

        - Auflösung ... was ist das? Im Web egal, 72, 300 bei Druck

        - Datei → `user.jpg` überschreiben

            - Qualität: 100 (ist ja klein)

            - Vorschau speichern weg

            - EXIF, XMP, IPTC weg

        **COMMIT:** <https://github.com/webmapping/nz/commit/b29eb126e6f9304f7f45df4dc3441d64ea700a67>

### Bilder im Header einbauen

- Einbau der Bilder diesmal ohne &lt;figure>-Element

    - Bannerbild als &lt;img>

        **COMMIT:** <https://github.com/webmapping/nz/commit/4a8a8e48a2d9a6df9a663e422e20227fc979c0ad>


    - Userbild als &lt;a>-Link auf das eigene github.com Repo mit &lt;img>

        ```html
        <header>
            <img src="images/header.jpg" alt="Blick auf den Tongariro mit Straße im Vordergrund">
            <a href="https://github.com/webmapping/nz"><img src="images/user.jpg" alt="VW Bus auf Wiese"></a>
        </header>
        ```

        **COMMIT:** <https://github.com/webmapping/nz/commit/6457b583717af67af209a87b96107fe046e2586e>

- Bildquellen im Header als Kommentare hinzufügen

    **COMMIT:** <https://github.com/webmapping/nz/commit/e2c951b850daef8e7ac485a069a2b511670d35c4>


## Leaflet Erstkontakt - wir brauchen eine Karte

- zuvor fügen wir unterhalb der &lt;figure> eine Überschrift &lt;h3> für die Karte hinzu

    ```html
    <h3>Übersichtskarte</h3>
    ```

    **COMMIT**: <https://github.com/webmapping/nz/commit/fb8ea94522fe46710da5dc28e6584d624283cc80>
- die Übersichtskarte erstellen wir nach dem Leaflet Tutorial unter <https://leafletjs.com/examples/quick-start/>

- wir folgen (mit leichten Modifikationen) dem Leaflet Tutorial

1. Leaflet CSS und Script im &lt;head>-Bereich einbauen

    - Stylesheet als &lt;link>-Element

    - Skript als &lt;script>-Element

    ```html
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin="" />

    <!-- Make sure you put this AFTER Leaflet's CSS -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
    ```

   **COMMIT:** <https://github.com/webmapping/nz/commit/d92151fa641b23cf125f2d0231ed89c12fe50e80>

2. Kartenfeld definieren

    - Kartenbereich als &lt;div> definieren

        - mit einem id-Attribut um die Karte aus dem Skript heraus ansprechen ("programmieren") zu können

        - einem style-Attribut mit CSS Regeln für die Größe des Kartenfelds und einem Rahmen - mehr zu CSS später

    ```html
    <h3>Übersichtskarte</h3>
    <div id="map" style="width:900px;height:600px;border:1px solid silver"></div>
    ```

   **COMMIT:** <https://github.com/webmapping/nz/commit/43bc6b6680d475c9ced7266d10b9f52592a494ad>

3. Kartenskript für eine Karte von London mit OpenStreetMap im Hintergrund

    - wir verwenden die Leaflet-Bibliothek, die wir in der Variablen **L** finden

    - Schritt 1: mit `L.map('map')` erzeugen wir im &lt;div> mit der ID "map" eine Leaflet-Karte und blicken über `.setView()` auf die Koordinaten in geographischer Breite/Länge im Zoomlevel 13. Diese Karte speichern wir in einer Variablen `var map` damit wir später noch Features hinzufügen können.

        ```javascript
        var map = L.map('map').setView([51.505, -0.09], 13);
        ```

    - Schritt 2: mit `L.tileLayer()`  erzeugen wir einen Hintergrundlayer der OpenStreetMap, setzen mit `maxZoom` den maximalen Zoomfaktor auf 19 und geben in `attribution` die Quelle des Layers an. Mit `.addTo(map)` wird der Hintergrundlayer an die Karte gehängt - wir sehen ihn jetzt im Browser und die Londonkarte ist damit sichtbar.

        ```javascript
        L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
            maxZoom: 19,
            attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
        }).addTo(map);
        ```

        **COMMIT**: <https://github.com/webmapping/nz/commit/525426f86ff90a9e4f3e968f01a599044dad5083>

    - Schritt 3: wir zentrieren die Karte auf die Wikipedia-Koordinate unserer Etappe. Dazu müssen wir nur die LAT/LNG Koordinaten bei `setView()` austauschen.

        ```javascript
        var map = L.map('map').setView([-39.2, 175.583333], 11);
        ```

        **COMMIT**: <https://github.com/webmapping/nz/commit/2244902aa9282ee8fa0aecadbe72249022736810>

    - Schritt 4: `L.marker()` definiert einen Marker an der gewünschten Koordinate und hängt ihn mit `.addTo(map)` an die Karte. Damit wir noch ein Popup hinzufügen können, merken wir uns diesen Marker in der Variablen `var marker`. Der Marker wird im Browser in der Karte angezeigt.

        ```javascript
        var marker = L.marker([-39.2, 175.583333]).addTo(map);
        ```

        **COMMIT**: <https://github.com/webmapping/nz/commit/f530393323661e7e2f8120bed43e72dd2ee0bc55>

    - Schritt 5: mit `marker.bindPopup()` definieren wir beim Marker ein Popup mit HTML-Inhalt und öffnen es mit `.openPopup()`. Das geöffnete Popup wird im Browser beim Marker in der Karte angezeigt.

        ```javascript
        marker.bindPopup("<b>Hello world!</b><br>I am a popup.").openPopup();
        ```

        **COMMIT**: <https://github.com/webmapping/nz/commit/0a0647ace7c55668dfce42af5594b4120d79cba6>

## HTML Üben: "Top Spots der Webmapper:innen"

- auf github.com ein neues Repo `top` erstellen, lokal clonen und über github pages online bringen

- `index.html`: selbe Seite wie die Etappe der Neuseelandreise nur mit anderen, beliebig wählbaren Inhalten

    - Texte von Wikipedia oder eigene

    - Bilder von <https://pixabay.com>, <https://commons.wikimedia.org> oder eigene

        - `images/header.jpg`:    1280 * 365

        - `images/user.jpg`:       100 * 100

        - `images/attraction.jpg`: 900 * NNN (Querformat)

    - Navigation zu selben Nachbar:innen wie bei der Neuseelandreise

- unser **COMMITS** siehe <https://github.com/webmapping/top/commits/main/>

- unsere erstellte Seite: <https://webmapping.github.io/top> (Online), <https://github.com/webmapping/top> (Repo)

## Oooops

Beim Erzeugen der Seitenstruktur für den Hauptinhalt wurde leider das  &lt;article>-Element vergessen. Wir ergänzen es bei beiden Repos direkt nach &lt;main> und schieben den bestehenden Inhalt dort hin. Die Struktur sollte bei beiden Beispielen dann so aussehen:

```html
<body>
    <header>
        <!-- Banner 1280x365 und Userbild 100x100-->
    </header>
    <main>
        <article>
            <!-- Inhalt der Seite mit Text, Bild, Karte und Links -->
        </article>
    </main>
    <footer>
        <!-- Navigation zu Nachbar:innen -->
    </footer>
</body>
```

**COMMIT** (nz): <https://github.com/webmapping/nz/commit/3a4eac5afe30627d7c83cc5872685ed5e49df9b4>

**COMMIT** (top): <https://github.com/webmapping/top/commit/1d2e354039133fb10e508327f15460ebf6c7e8af>
