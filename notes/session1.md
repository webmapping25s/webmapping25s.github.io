# Session 1 - GIT & HTML Erstkontakt (6.3.2025)

## github.com Account & Repo erstellen und clonen

- Account erstellen bei <https://github.com>

- Repo `nz` erstellen - <https://github.com/webmapping25s/nz> (... nicht NZ nZ Nz)

- beim Pulldown "*&lt;> Code*" die HTTPS Adresse kopieren - <https://github.com/webmapping25s/nz.git>

- Visual Studio Code starten

- über "*F1 / Git: Clone*" die HTTPS Adresse einkopieren und damit das Repo `nz` clonen

- Speicherort wählen und Repo als Folder öffnen

## HTML Seite erstellen

- neue `index.html` Datei im "*Explorer Bereich*" (oberstes Icon in der linken Symbolleiste) erstellen

    - *hmtl 5* Baustein einfügen

        - "*htm*" schreiben und dann das Template `html:5` auswählen und speichern

        - im "*Source Control Bereich*" (drittes Icon in der linken Symbolleiste) mit dem Plus rechts von `index.html` "*Stage Changes*" und mit einer sprechenden Commit-Message "*Commit*" drücken

        **COMMIT**: <https://github.com/webmapping25s/nz/commit/6edb8624bd6ec7a4176422ca94b96171560ccada>

    - **Achtung**: wenn E-Mail/Name noch nicht bekannt sind, kommt jetzt eine Fehlermeldung

        - "*Terminal / New Terminal*" aus dem Menü wählen und diese zwei Zeilen mit den eigenen Angaben dorthin kopieren und mit ENTER bestätigen

            ```shell
            git config --global user.email "email@example.com"
            git config --global user.name "username"
            ```

    - Sprache ändern

        ```html
        <html lang="de">
        ```

        **COMMIT**: <https://github.com/webmapping25s/nz/commit/978c8f6afcbb9eaad2d269103001549ed3b5cb32>

    - Titel ändern

        ```html
        <h1>Neuseelandreise</h1>
        ```

        **COMMIT**: <https://github.com/webmapping25s/nz/commit/f183124eab8a9bfebf7b4cd1d1332e34d787a671>

    - mit `F1 / Format document` können wir den Seitenquelltext sauberer formatieren

        **COMMIT**: <https://github.com/webmapping25s/nz/commit/ec4772916e44fbea9741143da66d48dd7deba1f3>

- Synchronisieren der Änderungen zurück nach <https://github.com/webmapping25s/nz>

    - im "*Source Control Bereich*" Rechts / Oben auf die drei Punkte klicken und **Push** auswählen - den Anordnungen folgen

    - damit sind die Änderungen bei <https://github.com/webmapping25s/nz> sichtbar

## Repo online stellen

- im Menü des Repos auf github.com "*Settings*" wählen

- Links "*Pages*" wählen

- unter "*Build and deployment*" bei "Branch" "*main*" wählen und "*Save*"

- damit ist nach kurzer Wartezeit das Repo unter <https://webmapping25s.github.io/nz> im Web erreichbar

- Vscode schließen und noch einmal alles herholen

    - "*File / Open Folder*" wählen, dann ist das Repo richtig geöffnet ("*File / Close Folder*" zum Schließen)

    - die History unserer Commit Nachrichten landet in der Timeline im linken Tab

    - auch Änderungen zur letzten Version sind im "*Source Control Bereich*" zu finden

    - Code formatieren wir mit "*F1 / Format document*"
  
    - Lokal Online gehen können wir mit einem Plugin

        - im "*Extensions Bereich*" (letztes Icon in der linken Symbolleiste) nach *liveserver* suchen und installieren

        - Rechts / Unten beim Symbol "*Go Live*" können wir unsere Webseite lokal im Browser öffnen. Alle Änderungen werden direkt nach dem Speichern angezeigt

## HTML Webseite weiter entwickeln

### Untertitel und Absätze mit formatiertem Text hinzufügen

```html
<h2>Tongariro Nationalpark</h2>
```

**COMMIT** <https://github.com/webmapping25s/nz/commit/7055d1e91659b949742d4a5b8b7c40992fc3ff3d>

- Tipp: VSCode zeigt Tooltips bei Elementen & Attributen. Links zu den MDN Web Docs (<https://developer.mozilla.org/>) sind sehr hilfreich

- Wikipedia-Suche "Tongariro-Nationalpark" bringt <https://de.wikipedia.org/wiki/Tongariro-Nationalpark>

- die ersten drei Absätze kopieren

    ```html
    <p>Der Tongariro-Nationalpark  ... Weltnaturerbe der UNESCO.</p>
    <p>Der Tongariro-Nationalpark ist ... Ruapehu (2797 m).</p>
    <p>Auf dem Gebiet des Nationalparks ... Vulkane als heilig verehren.</p>
    ```

    **COMMIT**: <https://github.com/webmapping25s/nz/commit/719cccba35eb55273a3341f87a4e69f4b05aa71f>

- fett / kursiv formatieren

    ```html
    <strong>Tongariro-Nationalpark</strong>
    <em>Weltnaturerbe der UNESCO</em>
    ```

    **COMMIT** <https://github.com/webmapping25s/nz/commit/ce417465bcbf81fe2a6f1ca049fa26f5a9974ed6>

    auch verschachtelt möglich, aber auf Reihenfolge beim Öffnen / Schließen der Tags achten!

    ```html
    <strong><em>Kultstätten der Māori</em></strong> (richtig)
    <strong><em>Kultstätten der Māori</strong></em> (falsch)
    ```

    **COMMIT**: <https://github.com/webmapping25s/nz/commit/84961abd6c3c47d8bd21ad1ec9bae7a675506a05>

- Link zur Wikipedia Quelle mit &lt;a> wie *anchor* und href-Attribut wie *hyperlink reference*, der Linktext steht dazwischen

    ```html
    <a href="https://de.wikipedia.org/wiki/Tongariro-Nationalpark">Quelle: Wikipedia</a>
    ```

    **COMMIT**: <https://github.com/webmapping25s/nz/commit/6fbc78b3da3a281aa06cf26c7b59e5d8572ecb1d>

### Bild der Emerald Lakes hinzufügen

- wir brauchen ein frei verfügbares Bild von <https://pixabay.com> oder <https://commons.wikimedia.org>

    - Suche bei Pixabay nach "Emerald Lakes" bringt <https://pixabay.com/photos/emerald-lakes-3189389/>

    - wir speichern direkt das Vorschaubild

    - Zielort: `images/attraction.jpg`

        **COMMIT**: <https://github.com/webmapping25s/nz/commit/45400b8fea3130ad7ee68a3251ccb1c1255259dc>

- Bild einbauen als &lt;figure> (Abbildung) und &lt;img> (Bild)

    - Bild als &lt;img> mit src-Attribut (source) für Bildquelle und alt-Attribut für alternativen Text, wenn das Bild nicht angezeigt wird - wichtig für Blinde!

        ```html
        <figure>
            <img src="images/attraction.jpg" alt="Drei Seen in einer Gebirgslandschaft">
        </figure>
        ```

        **COMMIT**: <https://github.com/webmapping25s/nz/commit/d0b6a539787b323e7e8ca02d67bd8e7a798cc369>

    - **kurzer Ausflug**: wie macht man Kommentare in HTML?

        ```html
        <!-- Bild von https://pixabay.com/de/photos/neuseeland-tongariro-crossing-83645/ -->
        ```

        **COMMIT**: <https://github.com/webmapping25s/nz/commit/4c1a4107b7ba1d6fb13b6f76c6ec1d75bd02b1db>

    - besser als Kommentare, die niemand sieht ist eine sichtbare Bildunterschrift als &lt;figcaption> mit einem &lt;a> Link zur Bildquelle

        ```html
        <figure>
            <img src="images/attraction.jpg" alt="Drei Seen in einer Gebirgslandschaft">
            <figcaption>Blick auf die Emerald Lakes mit dem Tongariro im Hintergrund (Quelle: <a href="https://pixabay.com/de/photos/neuseeland-tongariro-crossing-83645/">Pixabay</a>)</figcaption>
        </figure>
        ```

        **COMMIT**: <https://github.com/webmapping25s/nz/commit/8af6c65aede8e0fa26c80e2139b731d11148d70d>

### Linkliste mit drei beliebigen Links

- als &lt;ul> (unordered list) mit &lt;li> (list item) - z.B. Weblinks der Wikipedia-Seite verwenden

    ```html
    <ul>
        <li><a href="...">Link 1</a></li>
        <li><a href="...">Link 2</a></li>
        <li><a href="...">Link 3</a></li>
    </ul>
    ```

    **COMMIT**: <https://github.com/webmapping25s/nz/commit/9178ae4bbf3c5958f4ec3fa49634d972d0c52eb0>

### Etappennavigation zu den Nachbar:innen

- als &lt;nav> (navigation) mit Links - Nachbar:innen siehe OLAT Ordner `etappen.ods`

    ```html
    <nav>
        <a href="https://laurauniibk.github.io/nz">vorhergehende Etappe</a>
        <a href="https://webmapping25s.github.io/nz">diese Etappe</a>
        <a href="https://ernstanton.github.io/nz">nächste Etappe</a>
    </nav>
    ```

    **COMMIT**: <https://github.com/webmapping25s/nz/commit/bfc1166fb4f4d5189f538e7984f007219efa01a4>
