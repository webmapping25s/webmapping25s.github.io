# Session 3 - CSS entdecken mit main.css (20.3.2024)

## CSS entdecken mit einem Quiz

<https://webmapping.github.io/nz/quiz>

### Stylesheet einbinden

- wir haben ein Stylesheet vorbereitet unter <https://github.com/webmapping/nz/blob/main/quiz_main.css>

- über "*Raw*" kann der Code kopiert werden

- in `nz/index.html` im &lt;head> Bereich ein &lt;link> Element erzeugen und als href `main.css` eintragen

    ```html
    <link rel="stylesheet" href="main.css">
    ```

- STRG+Klick auf `main.css` ermöglicht uns, `main.css` an der richtigen Stelle zu erstellen

- dann den Inhalt unserer Vorlage dorthin kopieren, speichern und eure Seite sollte anders aussehen ;-)

    **COMMIT**: <https://github.com/webmapping/nz/commit/a35621fb05a820bc7dc6c04e551221c135c47d31>

- nachdem Breite, Höhe und Rahmen für das Kartenfeld jetzt in `main.css` definiert ist, können wir das `style`-Attribut beim map-DIV löschen

    **COMMIT**: <https://github.com/webmapping/nz/commit/6240ff79670f503af099c9fe768c36782f92b7f9>


### CSS Quiz in Dreierteams und einer halben Stunde

- wir analysieren die CSS-Regeln in `main.css` 

- Vorgaben und Fragen sind hier: <https://webmapping.github.io/nz/quiz>

    - TIPP: die Links zu den Fragen zeigen, wie man direkt auf Codezeilen bei github.com verweisen kann - für die Dokumentation eurer Projekte kann das später hilfreich sein

    - TIPP: mit der rechten Maustaste (oder länger drücken unter MAC) kann man Elemente der Seite im Browser "Untersuchen" und dabei unter anderem nicht nur den HTML Quellcode sondern auch die angewandten CSS-Regeln ein-/ausschalten bzw. verändern - das hilft beim Testen, was die einzelnen Attribute bewirken ...

### Frage und Antworten

1. was bewirkt die CSS Regel in Zeile [5-10](https://github.com/webmapping/nz/blob/main/quiz_main.css#L5-L10)?

    ```css
    body {
        font-family: 'Open Sans', sans-serif;
        background-color: #ffffff;
        max-width: 1280px;
        margin: auto;
    }
    ```

    Als `font-family` für die Seite wird "*Open Sans*" verwendet, ist dieser Font nicht verfügbar kommt eine serifenlose Standardschrift zum Einsatz; die Hintergrundfarbe (`background-color`) der Seite soll Weiß (`#ffffff`) sein; die Seite soll nie breiter sein als 1280px (`max-width`) und hat Links und Rechts den selben Rand (`margin: auto`) - dadurch wird der Seiteninhalt zentriert

2. wozu wird das Größer-als-Zeichen (`>`) in Zeile [12](https://github.com/webmapping/nz/blob/main/quiz_main.css#L12) verwendet?

    ```css
    header>img {
        max-width: 100%;
    }
    ```

   Das Größer-als-Zeichen (`>`) selektiert das erste Bild im Header, also unser Banner und setzt seine Breite auf 100% des Header-Bereichs

3. was passiert in der CSS Regel in Zeile [16-21](https://github.com/webmapping/nz/blob/main/quiz_main.css#L16-L21)?

    ```css
    header a img {
        border: 1px solid silver;
        border-radius: 50%;
        margin-top: -75px;
        margin-left: calc(50% - 50px);
    }
    ```

   Das quadratische User-Bild bekommt mit `border: 1px solid silver;` einen dünnen Rahmen, wird mit `border-radius: 50%;` als Kreis dargestellt, über `margin-top: -75px` nach oben in das Bannerbild geschoben und mit `margin-left: calc(50% - 50px);` in der Mitte positioniert. Die [CSS calc() Funktion](https://developer.mozilla.org/en-US/docs/Web/CSS/calc) erlaubt uns dabei das Rechnen mit Werten in CSS.

4. was passiert in der CSS Regel in Zeile [23-30](https://github.com/webmapping/nz/blob/main/quiz_main.css#L23-L30)?

    ```css
    main {
        max-width: 1024px;
        margin: auto;
        margin-top: -50px;
        border-left: 1px dashed gray;
        border-right: 1px dashed gray;
        border-bottom: 1px dashed gray;
    }
    ```

   Der Hauptbereich wird mit `max-width` auf die maximale Brei 1024px gesetzt, über `margin: auto` wieder zentriert und mit `margin-top: -50px` um 50px nach oben geschoben - damit ist der Abstand zum User-Bild nicht so groß. Rechts, Links und Unten wird mit `border-\[left|right|bottom]` ein gestrichelter Rand in grau mit 1px Breite gezeichnet (`1px dashed gray`)

5. was bewirkt "padding" in Zeile [33](https://github.com/webmapping/nz/blob/main/quiz_main.css#L33)?

    ```css
    article {
        padding: 1em;
    }
    ```

   Mit `padding` wird der gesamte Inhalt des Artikels um 1em vom Rand nach Innen verschoben - damit "klebt" die Schrift nicht mehr am gestrichelten Rand. Die Einheit `em` bezieht sich dabei die Schriftgröße und entspricht der Standard font-size 16px. Historisch kommt die Bezeichnung `em` aus dem Schriftsatz - mehr dazu hier: <https://de.wikipedia.org/wiki/Em_(Schriftsatz)>

6. wo werden die Fonts *Roboto* und *Montserrat* in Zeile [37](https://github.com/webmapping/nz/blob/main/quiz_main.css#L37) & Zeile [45](https://github.com/webmapping/nz/blob/main/quiz_main.css#L45) definiert?

    Die beiden Fonts werden in der dritten Zeile bei `@import` definiert und binden Webfonts ein, die uns Google-Fonts bereitstellt. Mehr dazu bei der nächsten Frage

    ```css
    @import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300&family=Open+Sans:ital@0;1&family=Roboto:wght@300&display=swap');
    ```

7. findet die Quelle aller verwendeten Fonts im Internet

    Die Quelle unserer Fonts ist <https://fonts.google.com/> - dort stehen 100e frei verfügbare Fonts für uns bereit. Einfach Browsen/Suchen, Anklicken und "*Get font*" drücken, dann landet der Font in unserer Auswahl, die wir Rechts/Oben beim Taschensymbol (*Tooltip View selected fonts*) ansehen können. Danach einfach weitersuchen und wenn wir alle gewünschten Fonts gesammelt haben, kann dort unter "*Get embeded Code*" mit der Option "*Web/@import*" die @import CSS Regel kopiert und ins Stylesheet `main.css` übertragen werden.

8. warum steht eine Raute (#) vor "map" in Zeile [52](https://github.com/webmapping/nz/blob/main/quiz_main.css#L52)?

    ```css
    #map {
        width: 90%;
        height: 600px;
        border: 1px solid silver;
        margin: auto;
    }
    ```

    Die Raute (#) ist der sogenannte **ID-Selektor** - er selektiert HTML-Elemente über deren eindeutige ID - in unserem Fall wird damit das Element mit `id="map"` angesprochen - unser Kartenfeld.

9. wofür stehen die vier Werte bei `padding` in Zeile [61](https://github.com/webmapping/nz/blob/main/quiz_main.css#L61)?

    ```css
    footer {
        text-align: center;
        padding: 1em 2em 3em 2em;
    }
    ```

    Die vier Werte bestimmen die Abstände des &lt;footer> Elements vom Artikelbereich und Seitenrand in der Reihenfolge Oben -> Rechts -> Unten -> Links (also im Uhrzeigersinn). Würden dort zwei Werte stehen wäre es Oben+Unten -> Rechts+Links

10. was bewirkt die CSS Regel in Zeile [64-67](https://github.com/webmapping/nz/blob/main/quiz_main.css#L64-L67)?

    ```css
    footer nav {
        display: grid;
        grid-template-columns: 40% 20% 40%;
    }
    ```

    `display: grid` bewirkt, dass die Navigationslinks im Footer in einem Grid-Layout dargestellt werden. Jedes &lt;a> Element landet in einem eigenen Bereich des Grids, das über `grid-template-columns` in drei Bereiche mit `40% 20% 40%` Breite eingeteilt wird.

11. welche Elemente werden mit dem Schatten in Zeile [76-78](https://github.com/webmapping/nz/blob/main/quiz_main.css#L76-L78) angesprochen?

    ```css
    header img,
    figure>img,
    #map {
        box-shadow:
            0 4px 8px 0 rgba(0, 0, 0, 0.2),
            0 6px 20px 0 rgba(0, 0, 0, 0.2);
    }
    ```

    Alle Bilder im Header (also Banner und User-Bild), jedes Bild eines &lt;figure> Elements und die Kartenfläche

12. was bewirkt die `@media` Regel in Zeile [81-90](https://github.com/webmapping/nz/blob/main/quiz_main.css#L81-L90)? Könnt ihr sie im Browser reproduzieren?

    ```css
    @media screen and (max-width: 1280px) {
        main {
            border: none;
        }

        figure {
            margin-left: 0;
            width: 100%;
        }
    }
    ```

    Die `@media` Regel bewirkt, dass der gestrichelte Rahmen des Artikels und die automatische Einrückung des Bilds innerhalb des &lt;figure> Elements entfernt wird, sobald die Seitenbreite im Browser unter 1280px fällt. Ausprobieren könnt ihr das durch Verändern der Größe des Browserfensters oder die Developer Tools des Browser - im Firefox z.B. F12 und dann Rechts/Oben das Icon für "*Bildschirmgrößen testen*" oder STRG+UMSCHALT+M oder im Menü "*Extras / Browser-Werkzeuge / Bildschirmgrößen testen*" wählen


## Font Awesome

- Font Awesome (<https://fontawesome.com/>) ist eine Icon-Bibliothek mit Hunderten von Webicons die wir in unseren Applikationen verwenden können. Um sie zu verwenden, müssen wir das Stylesheet der Bibliothek verlinken und können dann über Klassenattribute `class` bei &lt;i>-Elementen die einzelnen Icons einbauen.

- der Trick bei font Awesome ist, eine Schriftart zu verwenden, die anstelle von Buchstaben Icons darstellt.

- <!-- KI text :-) --> Der Nachteil daran ist, dass die Icons keine Informationen enthalten, die von Screenreadern oder Suchmaschinen gelesen werden können. Das bedeutet, dass wir die Icons nur verwenden sollten, wenn sie wirklich nur als dekoratives Element dienen. 
- als Alternative gibt es auch SVG-Icons, die wir in unserer Applikation einbauen können. Diese haben den Vorteil, dass sie von Screenreadern und Suchmaschinen gelesen werden können. 

- der Einbau des Stylesheets im &lt;head>-Bereich von `index.html` erfolgt über ein sogenanntes **CDN (Content delivery network)**. Es gibt viele solche CDNs, wir verwenden <https://cdnjs.com>

    - die Suche dort nach *font-awesome* bringt uns zu <https://cdnjs.com/libraries/font-awesome>

    - wir kopieren den Link-Tag (das mittlere der beiden Icons) von `all.min.css` und bauen ihn im &lt;head>-Bereich von `index.html` ein - was bedeuten die einzelnen Attribute?

        ```html
        <!-- Font awesome einbauen -->
        <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.7.2/css/all.min.css" integrity="sha512-Evv84Mr4kqVGRNSgIGL/F/aIDqQb7xQ2vcrdIwxfjThSH8CSR7PBEakCr51Ck+w+/U6swU2Im1vVX0SVk9ABhg==" crossorigin="anonymous" referrerpolicy="no-referrer" />
        ```

    - was bedeuten die Attribute?

        - `integrity`: zur Überprüfung, ob beim Laden alles funktioniert hat (i.e. die Datei vollständig)
        
        - `crossorigin`: keine Cookies (oder andere Authentifizierung) werden gesendet
        
        - `referrerpolicy`: keine Informationen über den Ursprung der Anfrage werden gesendet

- die Icons finden wir unter <https://fontawesome.com/icons>

    - **ACHTUNG**: nur die freien Icons verwenden (über den *Blitz* bei der Suche filtern) - die PRO-Icons werden nicht angezeigt


    - die Suche nach *camera* bringt uns ein *camera-retro*-Icon, dessen Code zum Einbauen wir direkt kopieren und an der passenden Stelle der HTML-Seite einsetzen können
    
        ```html
        <figcaption><i class="fa-solid fa-camera-retro"></i> Blick auf die Emerald Lakes ...</figcaption>
        ```

        **COMMIT**: <https://github.com/webmapping/nz/commit/b26de3f28df4f014d4f4d6e9fcbf7039b0bc77c9>

    - dasselbe machen wir mit Pfeilen für die Navigation (*fa-circle-left* und *fa-circle-right*) und einem Home-Symbol für die eigene Etappe (*fa-house-user*), die wir innerhalb der &lt;a>-Elemente direkt vor oder hinter die Textlabel setzen

        ```html
        <nav>
            <a href="#"><i class="fa-regular fa-circle-left"></i> vorhergehende Etappe</a>
            <a href="#"><i class="fa-solid fa-house-user"></i> diese Etappe</a>
            <a href="#">nächste Etappe <i class="fa-regular fa-circle-right"></i></a>
        </nav>
        ```

    - bei den beiden Überschriften für Übersichtskarte und Links passen *fa-map* und *fa-link*

        ```html
        <h3><i class="fa-regular fa-map"></i> Übersichtskarte</h3>
        <!-- .. -->
        <h3><i class="fa-solid fa-link"></i> Links</h3>
        ```

        **COMMITT**: <https://github.com/webmapping/nz/commit/6422ade2b29234567f487e0596bcea6680b56813>


## Script für Leaflet Karte nach main.js verschieben

Der Ausflug zu CSS ist vorläufig beendet, wir widmen uns jetzt wieder der Karte, deren Code noch in einem &lt;script>-Element in ` index.html` steht. Analog zur CSS-Datei ` main.css` können und werden wir ab jetzt **immer (!)** unseren Script-Code in eine eigene Datei auslagern - wir nennen sie ` main.js`

- das Erstellen funktioniert gleich wie bei `main.css`: wir fügen im &lt;head>-Bereich von `index.html` ein `script:src` Element hinzu, erzeugen es mit STRG-Klick und speichern es als `main.js` 

    ```html
    <script src="main.js"></script>
    ```

- dann verschieben wir den gesamten Script-Code in `index.html` zwischen &lt;script> und &lt;/script> nach `main.css`, löschen das nun leere &lt;script>-Element und speichern beide Dateien

- **HMMM**: leider wird die Karte nicht mehr angezeigt :-( Warum?

    - unser Script `main.js` wird im &lt;head> Bereich verlinkt und der Browser führt das Skript aus, sobald er es findet. Leider ist zu diesem Zeitpunkt aber der DIV für die Karte noch nicht "bekannt" und deshalb bekommen wir eine Fehlermeldung im Browser: mit `F12` können wir im Tab "Konsole" den Fehler sehen: `Uncaught Error: Map container not found` und noch einige Details dazu.

    - **!!!WICHTIG**: die Fehlerkonsole ist die erste und wohl wichtigste Anlaufstelle beim Suchen von Fehlern wenn wir mit Javascript/Leaflet programmieren - schaut immer dort hinein, denn oft steht die Lösung des Problems direkt dort.

    - in unserem Fall erfahren wir, dass der Map container nicht gefunden wird und mit einem Zusatzattribut beim &lt;script> können wir das beheben: "**defer**" ist das Zauberwort und bewirkt, dass die Ausführung des Skripts auf den Zeitpunkt "verschoben" wird, an dem der gesamte Inhalt der Seite vom Browser gelesen wurde. Damit ist dann auch der DIV der Karte verfügbar und die Karte kann gezeichnet werden

        ```html
        <script defer src="main.js"></script>
        ```

        **COMMIT**: <https://github.com/webmapping/nz/commit/cfaf8337dd35988f49e5a3ce320619a72b9dbae5>

## Vorschau Icon für den Browser-Tab

Die Karte ist damit fertig aber eine Fehlermeldung steht noch in der Konsole - scheinbar fehlt da noch ein `favicon.ico` - angezeigt wird dieses Icon im Browser beim Tab - wir verwenden unser User-Bild dafür und bauen es im &lt;head> Bereich als `link rel="shortcut icon"` ein

```html
<link rel="shortcut icon" href="images/user.jpg" type="image/x-icon">
```

**COMMIT**: <https://github.com/webmapping/nz/commit/4657676a48e33bab23b6afe1c0de53fa63116a19>
