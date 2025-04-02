# Session 4: Javascript Crashkurs

Heute werden wir in einem Javascript Crashkurs unsere Karte verfeinern und ein neues Feature implementieren.

## Kommentare

- helfen uns, im Skript die Übersicht nicht zu verlieren

- einzeilig mit `//`

- mehrzeilig mit `/*   */` - **Wichtig**: diese Art von Kommentar gilt auch bei CSS-Stylesheets

- ergänzen wir einen mehrzeiliger Kommentar ganz oben und vier einzeilige Kommentare vor den Codeblöcken

    ```javascript
    /*
    Skript für die Neuseelandreise
    */

    // Karte initialisieren

    // Hintergrundkarte definieren

    // Marker zeichnen

    // Popup definieren und öffnen
    ```

    **COMMIT** <https://github.com/webmapping/nz/commit/45c2b5eeb913291ba88de8692a4f9face54cc13b>

## Variablen

- **var** gibt es seit den Urzeiten von Javascript

- **let**, **const** sind viel moderner, seit [ECMAScript 6th Edition](https://262.ecma-international.org/6.0/)

- Variablen sind wie "*Boxen mit einem Label und einem Wert*" -> den Label können wir frei wählen (z.B. `zoom`), er darf nur keine Zahlen am Anfang haben, Leerzeichen beinhalten, oder ein [reserved word](https://262.ecma-international.org/6.0/#sec-reserved-words) sein (z.B. `function`, etc.)

    ```javascript
    let zoom = 11
    ```

    - Bestandteile der Variablendeklaration:

        - Keyword: `let`
        - Variablenname: `zoom`
        - Assignment operator: `=`
        - Wert: `11`

- Variablen sind case sensitiv! `zoom` ist nicht gleich `ZOOM`
        
- oft verwendet man [camelCase](https://en.wikipedia.org/wiki/Camel_case), z.B. `zoomFactorOfTheMap`

- oder auch Unterstriche, z.B. `zoom_factor`

- oder Abkürzungen, z.B. `zf`, allerdings nur wenn klar ist, was gemeint ist

- alles Großbuchstaben wie bei `ZOOM` verwenden wir für Konstanten und sonst besser nicht

- verwenden wir `let` statt `var`

    **VS Code Tipp**: beim Selektieren von Zeichenketten in VS Code reicht ein Doppelklick auf den Text. Dreifachklick selektiert sogar die ganze Zeile!

    ```javascript
    let map = L.map('map').setView([-39.2, 175.583333], 11);
    <!-- ... -->
    let marker = L.marker([-39.2, 175.583333]).addTo(map);
    ```

    **COMMIT**: <https://github.com/webmapping/nz/commit/0826f781ace98a381b0e091807ce8c2388304eae>

## Datentypen

### Number

- Zahlen gibt es in verschiedenen Varianten: Ganzzahlen (*Integer*) und Dezimalzahlen (*Float*). Als Dezimaltrennzeichen verwenden wir Punkte, nicht Kommas!

- speichern wir ganz oben im Skript, Breite, Länge und Zoomfaktor in drei Variablen `lat, lng` (*Float*), `zoom` (*Integer*) und verwenden wir sie immer dort, wo sie passen

    ```javascript
    let lat = -39.2;
    let lng = 175.583333;
    let zoom = 11;

    let map = L.map('map').setView([lat, lng], zoom);
    <!-- ... -->
    let marker = L.marker([lat, lng]).addTo(map);
    ```

    **COMMIT**: <https://github.com/webmapping/nz/commit/55d969c141df9100784e7cf645ab4bce4f1a3168>

### Strings

Zeichenketten kann man auf drei verschiedene Arten definieren

1. mit einfachen Anführungszeichen (*single quote*) wie bei `L.tileLayer`

    ```javascript
    let url = 'https://tile.openstreetmap.org/{z}/{x}/{y}.png';
    ```

2. mit doppelten Anführungszeichen (*double quote*) wie bei `bindPopup`

    ```javascript
    marker.bindPopup("<b>Hello world!</b><br>I am a popup.");
    ```

3. mit sogenannten *Backticks* die dem französischen *Accent grave* entsprechen und in unserem Beispiel noch nicht vorkommen. Der Vorteil von *Backticks* ist, dass sie auch Zeilenumbrüche innerhalb der Zeichenkette erlauben und wir in *Backticks* auch Variablen direkt verwenden können. Wir schreiben unser Popup mehrzeilig:

    ```javascript
    marker.bindPopup(`
        <b>Hello world!</b>
        <br>
        I am a popup.
    `);
    ```

    **COMMIT**: <https://github.com/webmapping/nz/commit/c6d0b3a157a78c98885670c866f9cab71dd4d318>

    - die Schreibweise von Zeichenketten mit *Backticks* (auch **Template literals** oder  **Template strings** genannt) werden wir in Zukunft sehr oft brauchen!

    - in **Template strings** können wir durch **String interpolation** mit `${}` Variablen direkt verwenden. Alles zwischen den geschwungenen Klammern von `${}` wird dabei als Javascript interpretiert
    
    - ergänzen wir beim Popup die Koordinaten für `lat` und `lng` mit jeweils 5-Nachkommastellen:

        ```javascript
        marker.bindPopup(`
            <b>Hello world!</b>
            <br>
            I am a popup at ${lat.toFixed(5)} / ${lng.toFixed(5)}.
        `);
        ```

        **COMMIT**: <https://github.com/webmapping/nz/commit/6300f54b1c6e75fd49b645931b2b14e935452f6a>


    - verwenden wir nochmals **Template strings** beim Popup für:
    
        - eine Überschrift 2 mit dem Namen der Etappe
        
        - eine ungeordnete Liste mit zwei Einträgen für die geographische Länge und Breite - lest beide Werte aus den Variablen `lat` und `lng` und fügt jeweils ein Grad-Zeichen hinzu

        - beide Koordinaten sollen wieder mit fünf Nachkommastellen angezeigt werden

            ```javascript
            marker.bindPopup(`
                <h2>Tongariro Nationalpark</h2>
                <ul>
                    <li>Breite: ${lat.toFixed(5)}°</li>
                    <li>Länge: ${lng.toFixed(5)}°</li>
                </ul>
            `);
            ```

        **COMMIT**: <https://github.com/webmapping/nz/commit/d5f766b4bcc28f249fb43eaecd196e2a7925b576>

### Objects - "object literals"

- sind Sammlungen von **Key/Value Paaren** mit spezieller Syntax

    - sie beginnen und enden mit geschwungenen Klammern `{}`

    - sie bestehen aus beliebig vielen, Komma getrennten "*Key-Value*" Paaren

    - *Key* und *Value* werden durch einen Doppelpunkt (`:`) getrennt

    - "*Keys*" sind einfache, sprechende Zeichenketten ohne Sonderzeichen (vorerst ...)

    - "*Values*" können alles sein, von Werten bis hin zu weiteren Objekten

- ein Beispiel für die console:

    ```javascript
    let course = {
        title: "Web mapping",
        semester: "Sommer 2025",
        stunden: 3,
        typ: "VU",
    }
    ```

- die Werte können über die sogenannte **Punktnotation** angesprochen werden:

    ```javascript
    console.log(course.title);
    console.log(course.semester);
    console.log(course.stunden);
    console.log(course.typ);
    ```

- wir löschen zuerst alle console-Beispiele und definieren dann ein Etappenobjekt `stop` mit Nummer (*nr*), Etappentitel (*title*), github User-Namen (*user*), Breite (*lat*), Länge (*lng*) und Zoomfaktor (*zoom*) und verwenden es anstelle der einfachen Variablen in unserem Kartenskript. Aus `lat` wird dann `stop.lat`, `lng` wird zu `stop.lng` und `zoom` ersetzen wir mit `stop.zoom` 

    ```javascript
    let stop = {
        nr: 8,
        title: "Tongariro Nationalpark",
        user: "webmapping",
        lat: -39.2,
        lng: 175.583333,
        zoom: 11,
    }
    ```

    **COMMIT**: <https://github.com/webmapping/nz/commit/0cdea8646f921eaef99741a5c6fe1619c1d43885>

- danach können wir das Etappenobjekt im Script verwenden: aus `lat` wird `stop.lat`, aus `lng` wird `stop.lng` usw.

    **COMMIT**: <https://github.com/webmapping/nz/commit/164eefcd50e56e1aa18903d6f0fd4f48a0cd8ad1>

### Objects - Spezialfall Arrays

- *Arrays* beinhalten Komma getrennte Listen von Werten, wie zum Beispiel die Koordinaten bei `setView`

- sie beginnen und enden mit **eckigen Klammern** `[]`

- die Werte können wieder alles sein können, also Werte, aber auch Objekte oder neue Arrays

- ein Beispiel für die console:

    ```javascript
    let coordinates = [-39.2, 175.583333];
    ```

- die Werte des Arrays können wir über Indizes ansprechen, die bei **0** beginnen und mit jedem Element um **1** erhöht werden

    ```javascript
    console.log(coordinates[0]);
    console.log(coordinates[1]);
    ```

- die Anzahl der Einträge innerhalb eines Arrays liefert uns `coordinates.length` 

- nachdem Arrays wie gesagt alles enthalten können, können sie natürlich auch Objekte enthalten - wir bauen uns einen Array mit allen Etappen-Objekten unserer Neuseelandreise. Jede(r) trägt sein Objekt dazu bei :-)

- Wir arbeiten alle gemeinsam und gleichzeitig hier: <https://etherpad.wikimedia.org/p/webmapping>

- den Array fügen wir als Konstante `STOPS` nach dem einzelnen Etappenobjekt `stops` ein und bereinigen allfällige Fehler, die uns VS Code anzeigt. Das Endresultat sollte so aussehen:

    ```javascript
    const STOPS = [
    {
        nr: 1,
        title: "Cape Reinga",
        user: "vintiyannick",
        lat: -34.42389,
        lng: 172.6775,
        zoom: 11,
    },
    // 21 weitere Einträge
    {
        nr: 29,
        title: "Kaikoura",
        user: "jessimeteo",
        lat: -42.4,
        lng: 173.68,
        zoom: 13,
    }
    ];
    ```

    **COMMIT**: <https://github.com/webmapping/nz/commit/164eefcd50e56e1aa18903d6f0fd4f48a0cd8ad1>

    **COMMIT** (Fehler bereinigt): <https://github.com/webmapping/nz/commit/68916b85b34ffdca68b2b636d237afa0bfa4342c>

- nachdem jetzt alle Etappen in Objekt `STOPS` verfügbar sind, können wir auch für alle Etappen Marker mit Popups zeichnen

- eine **for-Schleife** hilft uns, eine Etappe nach der anderen abzuarbeiten - wir schreiben sie vor den Code, der den Etappenmarker zeichnet und so sieht sie aus:

    ```javascript
    for (let i=0; i<STOPS.length; i++) {
        console.log(i, STOPS[i]);
    }
    ```

    - hier eine Definition der for-Schleife bei <https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Statements/for>:

        *Die for-Anweisung erstellt eine Schleife, die aus drei optionalen Ausdrücken besteht, die in Klammern eingeschlossen und durch Semikolons getrennt sind, gefolgt von einer Anweisung (normalerweise eine Block-Anweisung), die in der Schleife ausgeführt wird.*
    
    - die optionalen Anweisungen stehen für verschieden Phasen der Schleife

        - **initialization**: wird einmalig ausgewertet bevor die Schleife beginnt

        - **condition**: bestimmt, ob die Anweisung zwischen `{` und `}` ausgeführt werden soll

        - **afterthought**: legt fest, was nach jedem Schleifendurchgang passieren soll

- jetzt müssen wir nur noch den Code für Marker und Popup in die for-schleife verschieben und statt `stop.` auf den jeweiligen Wert in `STOPS[i].` zugreifen

    ```javascript
    // loop über Etappen
    for (let i = 0; i < STOPS.length; i++) {

        // Marker zeichnen
        let marker = L.marker([STOPS[i].lat, STOPS[i].lng]).addTo(map);

        // Popup definieren und öffnen
        marker.bindPopup(`
            <h2>${STOPS[i].title}</h2>
            <ul>
                <li>Breite: ${STOPS[i].lat.toFixed(5)}°</li>
                <li>Länge: ${STOPS[i].lng.toFixed(5)}°</li>
            </ul>
        `).openPopup();
    }
    ```

    **COMMIT**: <https://github.com/webmapping/nz/commit/51fd35c0ba84131547956596a641a33bc6948b44>

- beim Öffnen des Popups haben wir noch ein Problem, denn wir wollen es ja bei der eigenen Etappe öffnen und dann auch den eigenen Marker als Mittelpunkt der Karte anzeigen. Eine *if-Abfrage* hilft uns, das zu implementieren

## if-Abfragen

Bei *if-Abfragen* werden Anweisungen in Abhängigkeit von Bedingungen ausgeführt. Die Syntax besteht aus einem Keyword (z.B. `if`, `else if`, `else`) gefolgt von einer Bedingung (nicht bei `else` ...) in runden Klammern und einer Anweisung in geschwungenen Klammern, die ausgeführt wird, wenn die Bedingung zutrifft.

- die eigene Etappen können wir über den github User-Namen filtern:

    ```javascript
    // eigene Etappe fokussieren
    if (STOPS[i].user == "webmapping") {
        console.log("meine Etappe:", STOPS[i]);
    }
    ```

    **Wichtig**: zum Vergleichen verwenden wir doppelte Gleichheitszeichen (`==`)  und nicht Einfache (`=`), denn das einfache Gleichheitszeichen  würde `STOPS[i].user` den Wert *webmapping* zuweisen!

- als Anweisungen der *if-Abfrage* blicken wir mit `map.setView()` auf die eigene Etappe und öffnen danach mit `.openPopup()` das eigene Popup:

    ```javascript
    // eigene Etappe fokussieren
    if (STOPS[i].user == "webmapping") {
        map.setView([STOPS[i].lat, STOPS[i].lng], STOPS[i].zoom);
        marker.openPopup();
    }
    ```

    - `.setView()`-Code beim Initialisieren der Karte und die `.openPopup()`-Anweisung beim Marker können wir damit löschen

    **COMMIT**: <https://github.com/webmapping/nz/commit/1d7caaf70203b5b913c1f6086774f582a9ef2137>

## Inhalte der HTML-Seite dynamisch ändern

Mit Javascript können wir nicht nur unsere Karte konfigurieren, sondern auch den Inhalte der HTML Seite ändern. Als Beispiel erstellen wir unterhalb der Karte ein Pull-Down-Menü mit allen Etappen und wechseln zur entsprechenden Etappenseite, wenn der/die Benutzer:in im Pulldown eine andere als die eigene Etappe auswählt.

- das Pulldown bereiten wir unterhalb der Karte als &lt;div>-Element mit einem id-Attribut `pulldown` und einem &lt;select>-Element vor. Das &lt;select>-Element wird unser Menü mit den Etappeneinträgen beinhalten (siehe auch <https://developer.mozilla.org/de/docs/Web/HTML/Element/select>)

    ```html
    <div id="pulldown">
        <select></select>
    </div>
    ```

    **COMMIT**: <https://github.com/webmapping/nz/commit/18697cb2def6559a45f13978e00ba104658a0aa5>

- die Pull-Down-Einträge definieren wir in der *for-Schleife* unterhalb der *if-Abfrage*

    - mit `document.createElement()` erzeugen wir ein neues &lt;option>-Element, setzen dessen `value` auf den aktuellen User-Namen und den `text` der Anzeige auf das Muster "*Etappe Nummer: Titel*". Den eigenen Eintrag selektieren wir vor.

    - danach speichern wir mit `document.querySelector()` eine Referenz auf das Pulldown unterhalb der Karte und hängen mit `.appendChild()` den neuen Eintrag an das Menü. Wie der Name schon sagt, selektieren wir mit `document.querySelector()` Elemente in der HTML-Seite über CSS-Selektoren. `#pulldown select` steht dabei für: "*suche eine Element mit dem id-Attribut `pulldown` und darunter ein &lt;select>-Element.

    - der gesamte Code innerhalb der *for-Schleife* sieht damit so aus:

        ```javascript
        // Pull-Down-Menü befüllen
        let option = document.createElement("option");
        option.value = STOPS[i].user;
        option.text = STOPS[i].title;
        if (STOPS[i].user == "webmapping") {
            option.selected = true;
        }
        document.querySelector("#pulldown select").appendChild(option);
        ```

- das Pulldown ist damit fertig und wir können mit einem sogenannten **EventListener** auf Änderungen des Pull-Downs reagieren. EventListener sind nichts anderes als Funktionen, die an beliebigen HTML-Elementen angebracht werden können und dann ausgeführt werden, wenn beim Element ein bestimmtes Ereignis (*Event*) eintritt. In unserem Fall ist das Event der Wechsel beim Pulldown `.onchange`. Bei jedem Event wird der auszuführenden Funktion automatisch ein Event-Objekt übergeben (`evt`). Über `evt.target` können wir bequem auf das auslösende &lt;option>-Element zugreifen und uns dort über `.value` den User-Namen der neuen Etappe abholen. Mit ` window.location` weisen wir schließlich den Browser an, auf die Etappenseite des neuen Users zu Wechseln. **Puuuh**, ganz schön kompliziert, aber der Code den wir unterhalb (und außerhalb) der *for-Schleife* schreiben ist recht kurz und sprechend:

    ```javascript
    // auf Änderungen beim Pulldown reagieren
    document.querySelector("#pulldown select").onchange = function(evt) {
        let url = `https://${evt.target.value}.github.io/nz`;
        window.location = url;
    }
    ```

**COMMIT**: <https://github.com/webmapping/nz/commit/28fb523c1ea57d7cb670797ef8168f2e16cb0092>

- damit das Menü nicht so an der Karte klebt, ergänzen wir schließlich im Stylesheet `main.css` unterhalb der `#map`-Regel zwei einfache Stile, die das Pulldown um `5%` nach Rechts und `1em` nach Unten schiebt

    ```css
    /*
    das ist Teil der Workload 4 ;-)
    */
    ```

    **COMMIT**: <#>
