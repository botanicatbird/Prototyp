# Prototyp: scavengerHunt (Ein botanisches Cozy Game)

🌿 **[Live-Demo ansehen](https://botanicatbird.github.io/Prototyp/)**

---

## 1. Projektbeschreibung & Atmosphäre
scavengerHunt ist ein entspanntes, entschleunigtes Sammelspiel (inspiriert von der Ästhetik von *Tides: A Fishing Game* und dem Entdecker-Prinzip von *Pokémon Go*). Es richtet sich an Schüler der Mittelstufe sowie an naturinteressierte Menschen, mit dem primären Ziel, ein nachhaltiges Interesse für die Botanik zu wecken und die Faszination für die heimische Pflanzenwelt spürbar zu machen. Es gibt keinen Zeitdruck – der Fokus liegt auf dem gemütlichen Erkunden, dem spielerischen Entdecken und dem emotionalen Bezug zur Natur beim Vervollständigen des eigenen digitalen Herbariums.

---

## 2. Der Homescreen (Cozy Nintendo-Karte)
Der Startbildschirm ist das visuelle Herzstück des Spiels und **immer sichtbar** – er zeigt die stilisierte GPS-Karte mit dem eigenen Standort.

- **Stilisierte GPS-Karte:** Eine vereinfachte, wunderschöne Karte basierend auf dem echten Standort des Nutzers. Straßen sind sanfte Sandpfade, Grünflächen sind in weichen Pastell-Grüntönen gehalten.
- **Freie Wiese:** Die SVG-Bäume wurden bewusst entfernt, um Platz für die wachsenden Pflanzen-PNGs auf der Karte zu schaffen.
- **Lebendiges Herbarium auf der Karte:** Orte, an denen der Nutzer bereits Pflanzen bestimmt hat, werden dauerhaft mit einem PNG-Pin markiert. Klick → Mini-Karte.
- **Dynamisches Design:** Die Jahreszeit verändert Karten-Farbgebung und Badges.
- **Standort-Pulse:** Ein animierter grüner Punkt zeigt den aktuellen Standort an.
- **Florin:** Wandert frei über den Homescreen (JS-gesteuert, CSS-Transition). Sprechblase zeigt aktive Aufgaben oder allgemeine Einladung (siehe Abschnitt 6).

---

## 3. Ziel des Prototypen (MVP)
Da dies ein erster Prototyp ist, simuliert der Code die GPS-Karte: Es wird eine interaktive Mockup-Karte (SVG mit Straßen, Bäumen und anklickbaren Pflanzen-Pins) dargestellt, auf der man das Spielprinzip testen kann, ohne dass echtes GPS-Tracking aktiv sein muss.

### Karten-Pin Logik
- **Sichtbarkeit:** Auf der Karte werden ausschließlich Pflanzen angezeigt, die bereits vollständig bestimmt und im Herbarium eingetragen sind. Noch nicht gefundene oder nur gesammelte (Körbchen) Pflanzen erscheinen nicht als Pin.
- **Ins Körbchen nur via Kamera:** Pflanzen gelangen ausnahmslos über den Kamera-Button (Scan-Simulation) ins Körbchen – niemals durch Antippen der Karte.
- **Anklicken eines Pins:** Öffnet eine kleine Mini-Karte (Bottom Sheet) mit Name, lateinischem Namen und einem direkten Link zum vollständigen Herbariumseintrag. Es gibt dort keine „Ins Körbchen"-Aktion.

---

## 4. Asset-basiertes Icon-System

Das Interface verwendet ein **zweigeteiltes Icon-System**:

### Navigations- und FAB-Icons — eigene PNG-Grafiken
Alle sichtbaren Haupt-Buttons nutzen transparente PNG-Assets aus dem Ordner `assets/icons/`:

| Asset-Datei | Button | Funktion |
|-------------|--------|----------|
| `assets/icons/Einstellungen.png` | Settings-Button | Einstellungen (Platzhalter) |
| `assets/icons/Koerbchen.png` | Nav-Pill | Körbchen – gesammelte, unbestimmte Pflanzen |
| `assets/icons/Buch.png` | Nav-Pill | Herbarium – die vollständige Pflanzensammlung |
| `assets/icons/ToDoListe.png` | Nav-Pill | To-Do Liste – Suchaufträge & Fortbildungsaufträge |
| `assets/icons/Camera.png` | Scan-FAB (Mitte unten) | Kamerasucher öffnen, Pflanze scannen |
| `assets/icons/Mikroskop.png` | Bestimmungsschlüssel-Titel | Panel-Header des Bestimmungsschlüssels |
| `assets/icons/Fruehblueher.png` | Herbarium-Abschnitt | Kategorie Frühblüher |
| `assets/icons/Herbstblueher.png` | Herbarium-Abschnitt | Kategorie Herbstblüher |
| `assets/icons/Winterblueher.png` | Herbarium-Abschnitt | Kategorie Winterblüher |
| `assets/icons/sun.png` | Herbarium-Abschnitt + Sommer-Badge | Kategorie Sommerblüher + aktive Welt |
| `assets/icons/Blatt.png` | Level-Badge | Level-Anzeige oben rechts |

CSS-Klassen: `.nav-icon-img` (42 × 42 px in der Pill, 36 × 36 px für Settings) und `.fab-icon-img` (48 × 48 px). Hover: `scale(1.10) + brightness(1.12)`. Active: `scale(1.06) + white glow`.

### Inline-Icons — Lucide (CDN)
Lucide Icons (via CDN: `https://unpkg.com/lucide@latest`) werden weiterhin für kleine dekorative Icons in Panel-Inhalten genutzt (z. B. `leaf` und `flower-2` in den Top-Badges, `book-open` in der Karten-Minikarte, `lock` in gesperrten Herbarium-Karten, `image` als Fallback in Quiz-Kacheln). **Nicht mehr** für den Shutter-Button — dieser nutzt jetzt `assets/icons/Camera.png` (Klasse `.shutter-icon`, 34 × 34 px). `lucide.createIcons()` wird nach jedem dynamischen Render-Schritt erneut aufgerufen.

---

## 5. Navigation & Layout

Die Navigation ist als **schwebende Pill-Leiste** auf der linken Seite des Bildschirms positioniert (kein Bottom-Tab-Bar). Alle Inhalte öffnen sich als Panels über der Karte. Panels haben `z-index: 60` und symmetrisches Padding — sie überdecken die Nav-Pill vollständig. Das Quiz-Panel hat zusätzlich `justify-content: center` und eine feste `max-height`, sodass kein Scrollen nötig ist.

> Der Botanik-Assistent ist **nicht** in der Nav-Leiste vertreten. Der Zugang erfolgt ausschließlich über **Florin** (siehe Abschnitt 6).

**Kamera-FAB:** Der Kamera-Button (`assets/icons/Camera.png`) unten in der Mitte des Bildschirms öffnet einen Kamerasucher-Platzhalter mit Shutter-Button – damit werden neue Pflanzen fotografiert und ins Körbchen gelegt.

---

## 6. Florin – Der Pedagogical Agent

**Florin** ist der kleine Gärtner-Charakter des Spiels und fungiert als *Pedagogical Agent* direkt auf dem Homescreen. Er ersetzt den früheren Chat-Button in der Navigationsleiste und ist stattdessen als eigenständiges, lebendig animiertes Element auf der Karte verankert.

### Rolle & Konzept
Florin ist mehr als ein Chatbot-Shortcut – er repräsentiert den pädagogischen Ansatz des Spiels: Lernen durch eine freundliche, vertraute Begleitfigur. Das Konzept orientiert sich an der Forschung zu *Pedagogical Agents* (z. B. Vygotskys Zone of Proximal Development), bei dem ein animierter Charakter den Lernenden durch Aufgaben führt und bei Bedarf Hilfe anbietet.

### Technische Umsetzung
- **Position:** Wird vollständig per JS gesetzt (`style.left`, `style.bottom`). CSS-Fallback `right: 20px; bottom: 105px` gilt nur bis zum ersten Frame. `z-index: 55` – bleibt über Panels sichtbar.
- **Asset:** `assets/Gardener.png` – transparentes PNG des Gärtner-Charakters.
- **Wandern:** `initFlorinWander()` setzt die Startposition und startet einen `setInterval` (~12–16 s). `florinPickNewSpot()` wählt zufällige Koordinaten im sicheren Bereich (rechts vom Nav-Pill, außerhalb der Scan-FAB-Zone, obere Bildschirmhälfte bis 52 % Höhe). CSS-Transition `left 8 s / bottom 7 s cubic-bezier(0.45,0.05,0.55,0.95)` erzeugt das sehr sanfte, träge Gleiten.
- **Animationen:**
  - `florinBob` auf `.florin-img` (3 s, ease-in-out, infinite): Schweben + Schaukeln + Atemstrecken.
  - `bubbleFloat` auf `.florin-bubble`: Sprechblase pulsiert.
  - **Interaktionszustand:** Klick → `togglePanel('chatbot')` → `.active`-Klasse → orangefarbener Glow, Bob pausiert, Bubble ausblendet.
- **Florin bei Panels:** Wenn ein Panel (außer Chatbot) geöffnet wird, wird Florin per `florinHide()` vollständig ausgeblendet (`opacity: 0`, `pointerEvents: none`) — er ist weder sichtbar noch anklickbar. Beim Schließen ruft `florinResumeWander()` `florinShow()` auf und wählt danach einen neuen Wanderspot.
- **Florin bei Karten / Popups:** Gleiche Logik — `florinHide()` / `florinShow()`. Aufgerufen von `showMapPinCard()`, `closeMapPinCard()`, `showPlantPopup()`, `closePopupDirect()`.
- **Overlay-Infrastruktur (vorbereitet, deaktiviert):** `.florin-figure` → `.florin-img` + `#florinEquipLayer` (absolut, leer). Die gesamte Logik (`renderFlorinEquipment`, `toggleEquip`, `overlayPos` je Item) ist im Code vorhanden, aber alle Items haben `equipped: false`. Florins Homescreen-Modell zeigt ausschließlich das Original-PNG (`assets/Gardener.png`) – keine Overlays. Die Ausrüst-Funktion wird in einem späteren Schritt mit ausgereifter UX-Logik freigeschaltet.
- **`assets/Trophies/`** wird ausschließlich für die **visuellen Sammlerstücke im Herbarium-Tab** genutzt. `apron.png` und `pinbadge.png` erscheinen dort als Vorschau-Bilder freigeschalteter Belohnungen (PNG-Icon + „✓ In deiner Sammlung"-Badge). Gesperrte Items zeigen Lucide-Fallback-Icons.

### Inhalt des Chatbot-Panels (bereinigt)
Das Panel ist nun **clean** – keine statischen Karten mehr. Nur zwei Bereiche:
1. **Aktuelle Lektion (dynamisch):** `div#florinTaskSection` ist standardmäßig `display:none`. `renderFlorinTasks()` ruft `getActiveFortbildung()` auf — gibt es eine nicht abgeschlossene Lektion, erscheint sie als **große, anklickbare Karte** mit Level-Badge, Titel, Beschreibung und „Lektion starten →"-Button. Sind alle Lektionen abgeschlossen: komplett unsichtbar.
2. **Freitextchat:** Direkt nach dem Öffnen sichtbar. Florin antwortet auf botanische Fragen.

### Sprechblasen-Logik
`updateFlorinBubble()` wird in `init()` und bei jedem Wander-Zyklus (`florinPickNewSpot()`) aufgerufen:

| Situation | Bubble-Verhalten |
|-----------|-----------------|
| Aktive Lektion vorhanden | immer sichtbar: `🔬 Neue Lektion wartet!` |
| Level 1–2, keine aktive Lektion | Bubble komplett ausgeblendet |
| Level 3+, keine aktive Lektion | alle **3 Wander-Zyklen** einmal sichtbar: `Du kannst mich alles fragen!` |

`_florinBubbleCycleCount` (let, Startwert 0) wird in `florinPickNewSpot()` inkrementiert. Die Modulo-3-Prüfung bestimmt die gelegentliche Sichtbarkeit.

---

## 7. Die App-Bereiche (Panels)

### Panel 1: „Das Körbchen"
- Zeigt alle gesammelten, noch **unbestimmten** Pflanzen.
- **Anonyme Platzhalternamen:** Solange eine Pflanze noch nicht bestimmt wurde, wird ihr echter Name im Körbchen **nicht** angezeigt:
  - ⭐⭐⭐⭐+ (stars ≥ 4) → `Seltener Fund ✦`
  - ⭐⭐⭐ (stars = 3) → `Geheimnisvoller Fund`
  - ⭐⭐ oder weniger → `Unbekannte Pflanze`
- Das Pflanzenfoto wird leicht entsättigt und weich geblurt dargestellt, um die Identität zu verschleiern.
- Neben jeder Pflanze befindet sich ein **„Jetzt bestimmen"-Button**, der direkt den Bestimmungsschlüssel öffnet.
- **Icon:** `assets/icons/Koerbchen.png` – keine Emojis.

---

## 8. Didaktisches Bestimmungs-Quiz

### Übersicht und pädagogischer Ansatz
Der Bestimmungsschlüssel ist das Herzstück des Lernzyklus: **Sammeln → Vergleichen mit eigenen Fotos → Bestimmen nach wissenschaftlichem Schlüssel → Sofortige Belohnung im Herbarium.** Statt eines zufälligen Multiple-Choice-Formats führt ein echter zweistufiger botanischer Entscheidungsbaum durch die Familienbestimmung.

### 8.1 UI-Layout (von oben nach unten)

Das Quiz-Panel zeigt folgende Elemente in dieser Reihenfolge:

1. **✕-Schaltfläche (oben rechts)** — schließt das Panel jederzeit ohne Datenverlust (`.panel-close-btn`).
2. **Große Foto-Kachel** (`.quiz-photo-tile`, Seitenverhältnis 4:3) — zeigt die eigenen, beim Scan aufgenommenen Pflanzenfotos als simulierte Galerie.
3. **Fortschrittsbalken** — zeigt „Schritt 1 / 2" bzw. „Schritt 2 / 2" mit animiertem grünen Balken.
4. **Fragen-Sprechblase** (`.quiz-question-bubble`) — Schrift Lora/kursiv, Bubble-Style (abgerundete Ecken, Ecke unten links spitz), weißlicher Hintergrund.
5. **Antwort-Kacheln** (`.quiz-options-grid`, 2-Spalten-Raster) — jede Kachel zeigt ein botanisches Illustrationsbild (72 × 72 px, kein Hintergrund, nur Drop-Shadow), darunter Optionsname und Beschreibung.
6. **Florin-Tipp** (`toggleQuizFlorinHint()`) — ein-/ausklappbares Hilfe-Panel mit Florins Avatar und motivierendem Text.

### 8.2 Foto-Kachel: Navigation, Swipe & Zoom

Die Foto-Kachel zeigt **4 Slides** — identisch zur Herbarium-Galerie:

| Index | Inhalt | Transform |
|-------|--------|-----------|
| 0 | Pflanzenmodell | `scale(1.0)` — clean, kein Foto-Filter |
| 1 | Blütenaufnahme · Eigenes Foto | `scale(1.18) translateY(-6%)` |
| 2 | Blattaufnahme · Eigenes Foto | `scale(0.92) rotate(-4deg)` |
| 3 | Gesamtaufnahme · Eigenes Foto | `scale(1.0)` + Foto-Filter |

**Klick-Pfeile (Desktop):** `.gallery-arrow`-Buttons links/rechts der Kachel rufen `quizPhotoNav(±1)` auf — gleiche Optik wie im Herbarium.

**Swipe-Geste (Touch):**
- `ontouchstart` speichert `_quizTouchStartX`
- `ontouchend` vergleicht deltaX: > 42 px → `quizPhotoIndex` ± 1 zyklisch (0–3, Modulo `GALLERY_TOTAL`) → `_updateQuizPhoto()`
- Beim Foto-Wechsel wird ein aktiver Zoom automatisch zurückgesetzt

**Doppeltipp-Zoom:**
- `onclick` prüft Zeit seit letztem Tap (`_quizLastTap`, Schwellwert 320 ms)
- Doppeltipp → `quizPhotoZoomed` toggle → `scale(2.5) translateY(-8%)` für Detailansicht
- Hint-Label wechselt zwischen `⊕ Einzoomen` und `× Verkleinern`

**Vier Punkte-Navigation** (`.quiz-photo-dot`) zeigen den aktiven Slide. Label (`quizPhotoLabel`) nennt Slide-Typ.

### 8.3 Entscheidungsbaum (`quizTree`)

Dreiknotiger botanischer Bestimmungsschlüssel. Knoten mit `next`-Feld leiten zur nächsten Stufe weiter, Knoten mit `family`-Feld sind Endknoten:

```
L1 — Blütenform (4 Optionen)
├── A: ≥ 5 Kronblätter → L2A
├── B: 4 kreuzförmige Kronblätter → terminal: Brassicaceae (Kreuzblütler)
├── C: Andere Form → L2B
└── SPECIAL: Etwas ganz anderes → Auflösung durch Florin

L2A — 5+ freie Kronblätter (3 Optionen)
├── A1: Hahnenfußgewächse → terminal: Ranunculaceae
├── A2: Rosengewächse → terminal: Rosaceae
└── SPECIAL: Etwas ganz anderes → Auflösung durch Florin

L2B — Andere/komplexe Blütenform (5 Optionen)
├── B1: Doldenblütler → terminal: Apiaceae
├── B2: Korbblütler → terminal: Asteraceae
├── B3: Schmetterlingsblütler → terminal: Fabaceae
├── B4: Lippenblütler → terminal: Lamiaceae
└── SPECIAL: Etwas ganz anderes → Auflösung durch Florin
```

**Sonderoption „Etwas ganz anderes"** (`special: true`): Passt keine Familie, wählt der Nutzer diese Option. `_showQuizReveal()` zeigt Pflanzenbild, Name, lateinischen Namen und Familie direkt an — Florin erklärt es kurz. Danach „Ins Herbarium aufnehmen"-Button → `completeIdentification()`. Gedacht für Pflanzen mit seltenen Familien (z. B. Sommerlinde / Malvaceae).

**Familienabgleich** (`familyMatches()`): Extrahiert den wissenschaftlichen Namen in Klammern aus dem `p.family`-String — `"Hülsenfrüchtler (Fabaceae)"` → `"Fabaceae"` — und vergleicht mit dem `family`-Feld des gewählten Endknotens.

**Bilder** in `assets/quiz/`:

| Datei | Verwendung |
|-------|-----------|
| `Hahnenfussgewaechse.png` | L2A Hahnenfußgewächse |
| `Rosengewaechse.png` | L2A Rosengewächse |
| `Kreuzbluetler.png` | L1 Option B (Kreuzblütler) |
| `Doldenbluetler.png` | L2B Doldenblütler |
| `Korbbluetler.png` | L2B Korbblütler |
| `Schmetterlingsbluetler.png` | L2B Schmetterlingsblütler |
| `Lippenbluetler.png` | L2B Lippenblütler |

L1 Option A (5 Kronblätter) zeigt `Hahnenfussgewaechse.png` und `Rosengewaechse.png` diagonal übereinander als Vorschau. L1 Option C (Andere Blütenform) und „Etwas ganz anderes" zeigen kein Bild bzw. ein `?`.

### 8.4 Florin-Hilfe-System

Jeder `quizTree`-Knoten hat ein `florinTip`-Feld. Der Tipp ist **nicht** automatisch sichtbar, sondern wird auf Wunsch eingeblendet:

- **„? Nicht erkennbar? Florin hilft!"-Button** (`.quiz-hint-btn`): volle Breite, rund, dezent. Klick → `toggleQuizFlorinHint()`.
- **Florin-Tipp-Panel** (`.quiz-florin-tip`): erscheint animiert (`fadeInUp`) über dem Button. Zeigt `assets/Gardener.png` als kleines Avatar-Bild + kursiven Tipp-Text. Zweites Klicken blendet ihn wieder aus.
- Die Tipps sind pädagogisch formuliert: keine trockenen Fachinfos, sondern Eselsbrücken und persönliche Begeisterung („Und weißt du was? Äpfel, Erdbeeren und Rosen sind alle in dieser Familie!").

### 8.5 Feedback bei falscher Antwort

`_showQuizRetry()` zeigt:
- Freundliche Fehlermeldung: „Noch nicht ganz … 🌱" mit Erklärung welche Familie es *nicht* ist
- Grüner Tipp-Kasten mit Blütezeit + Habitat der aktuellen Pflanze
- „Nochmal versuchen →"-Button → `quizState = { node:'L1', path:[] }` → `renderQuizPage()`

Kein Game Over, kein XP-Verlust. Der Baum beginnt immer von Stufe 1 neu.

### 8.6 Belohnungs-Workflow (Direkter Steckbrief nach Bestimmung)

Nach korrekter Bestimmung läuft folgender Ablauf:

1. `_showQuizSuccess()` → kurze Erfolgskarte mit Pflanzenbild, Name, Familienbeschreibung → nach 1,9 s `completeIdentification(id)`.
2. `completeIdentification(id)`:
   - Pflanze aus `basket[]` entfernen, in `herbarium[]` aufnehmen, `plants[id].inHerbarium = true`
   - XP gutschreiben, Badges + To-Dos aktualisieren, Karten-Pins neu rendern
   - Konfetti starten
   - Erfolgsbildschirm rendern: Pflanzenbild (96 × 96 px), Name, lateinischer Name, XP-Badges, Fun-Fact-Auszug
   - **Nach 2,2 s**: `closePanel('quiz')` → `showPlantPopup(id)` direkt
3. `showPlantPopup(id)` öffnet sofort den vollständigen Pflanzensteckbrief (4-Slide-Galerie, Systematik, Lore).
   - Am unteren Ende des Steckbriefs: dezenter Textlink **„Im Herbarium ansehen →"** (nur wenn `inHerb`). Klick: `closePopupDirect()` → `openHerbariumAtPlant(id)`.
4. `openHerbariumAtPlant(id)` (nur bei explizitem Klick auf den Link):
   - `_activatePanel('herbarium')` → rendert Herbarium neu
   - Nach 400 ms: `querySelector('.herb-card[data-id="${id}"]')` → `scrollIntoView` → `.herb-card-highlight` (2,2 s)
   - Kein automatischer Popup mehr aus dieser Funktion.

**Ergebnis:** Nach erfolgreicher Bestimmung erscheint sofort der Steckbrief — kein Warten auf Herbarium-Animationen. Wer die Karte im Herbarium sehen möchte, nutzt den optionalen Link am Ende des Steckbriefs.

---

## 9. Fortbildungsaufträge – Lern-Sequenz

### Konzept
Fortbildungsaufträge sind geführte Lektionen, die Florin dem Spieler in einer strikten Reihenfolge anbietet. Es ist immer **genau eine Lektion** gleichzeitig aktiv. Eine neue Lektion wird erst freigeschaltet, wenn die vorherige abgeschlossen wurde.

### Stand der Lektionen

| Level | ID | Titel | Status |
|-------|----|-------|--------|
| 1 | `bluetenaufbau` | Blütenaufbau | ✅ vollständig implementiert |
| 2 | `bluetenformen` | Blütenformen | 🔒 noch nicht implementiert |
| 3 | `bluetenstaende` | Blütenstände | 🔒 noch nicht implementiert |
| 4 | `blattstellungen` | Blattstellungen | 🔒 noch nicht implementiert |
| 5 | `blattformen` | Blattformen | 🔒 noch nicht implementiert |

---

### Lektion 1: Blütenaufbau (✅ spielbar)

Der Einstieg erfolgt über Florins Chat-Panel → Lektion-Karte → „Lektion starten". Das Puzzle öffnet sich als Vollbild-Overlay (`#puzzleOverlay`) über der gesamten App.

**Ablauf:** Das Puzzle ist in 4 Schritte gegliedert. Pro Schritt muss der Nutzer das richtige Blütenteil per Drag & Drop in die Schablone ziehen. Ist ein Schritt abgeschlossen, ersetzt eine neue Schablone die alte — das platzierte Teil erscheint direkt eingezeichnet. Am Ende wird die fertige Lösungsgrafik eingeblendet.

| Schritt | Zu platzieren | Neues Schablonenbild |
|---------|--------------|---------------------|
| 1 | Stempel | `Bluetenaufbau_Schablone2.png` |
| 2 | Staubblätter | `Bluetenaufbau_Schablone3.png` |
| 3 | Kronblätter (3×) | `Bluetenaufbau_Schablone4.png` |
| 4 | Kelchblätter (3×) | `Bluetenaufbau_Loesung.png` |

**Blütenteile und ihre Startpositionen:**
- Oben links: Kelchblatt1 — oben Mitte: Stempel — oben rechts: Staubblätter
- Links neben Schablone: Kelchblatt2 — rechts neben Schablone: Kelchblatt3
- Unten: Kronblatt1, Kronblatt2, Kronblatt3

**Gesperrte Teile:** Blütenteile, die noch nicht an der Reihe sind, werden grau dargestellt (Grayscale-Filter) und können nicht gezogen werden — ohne Transparenz-Hinweis.

**Florin:** Erscheint oberhalb der Schablone mit einer Sprechblase. Erklärt bei jedem Schritt den aktuellen Blütenteil didaktisch, z. B. Fruchtknoten, Griffel und Narbe beim Stempel.

**Fortschritt:** Punkte-Leiste am unteren Rand zeigt den aktuellen Schritt. Nach Abschluss aller Schritte erscheint der „Lektion abschließen"-Button.

**Assets (`assets/Fortbildung/`):**
`Bluetenaufbau_Schablone1–4.png`, `Bluetenaufbau_Loesung.png`, `Stempel.png`, `Staubblaetter.png`, `Kronblatt1–3.png`, `Kelchblatt1–3.png`

---

### Abschluss-Workflow (alle Lektionen)
1. Nutzer klickt „Lektion abschließen" → `completeLernSession(id)`
2. `lesson.completed = true` → nächste Lektion wird zur aktiven
3. XP wird addiert, Level-Badge aktualisiert
4. Kurzer Erfolgs-Toast erscheint
5. `updateFlorinBubble()` + `renderFlorinTasks()` aktualisieren Bubble und Aufgabenliste

---

## 10. Panel: „Das Herbarium"
Unterteilt in **3 Reiter (Tabs)**:
1. **Nach Welten / Blütezeit:** Vier feste botanische Kategorien, vollständig dynamisch durch `renderHerbarium()` gerendert:
   - **Frühblüher** (grün, `assets/icons/Fruehblueher.png`): Bärlauch, Kirschbaum, Apfelbaum, Schlüsselblume, Gänseblümchen
   - **Sommerblüher** (orange, `assets/icons/sun.png`): Wiesen-Rotklee, Vielblättrige Lupine, Gefleckte Taubnessel, Goldnessel, Weiße Taubnessel, Kriechender Hahnenfuß, Sommerlinde, Gänseblümchen
   - **Herbstblüher** (dunkelorange, `assets/icons/Herbstblueher.png`): Herbstzeitlose, Efeu
   - **Winterblüher** (blau, `assets/icons/Winterblueher.png`): Schneeglöckchen, Vogelmiere, Greiskraut
   - Pflanzen ohne Daten und noch nicht bestimmte Pflanzen erscheinen als gesperrte Karten mit `???`.
   - Das Gänseblümchen erscheint in beiden Welten (Frühblüher + Sommerblüher), da es fast ganzjährig blüht.
2. **Nach Pflanzenfamilien:** Sortiert nach botanischer Verwandtschaft.
3. **Trophäen:** Dynamisch durch `renderTrophyTab()`. Gesperrte Items zeigen Unlock-Bedingung. Bei Freischalten einer neuen Trophäe erscheint `#trophyUnlockToast` — eine zentrierte Benachrichtigung mit PNG, Name und Beschreibung (3,2 s sichtbar).

**Herb-Card `data-id`:** Jede Karte trägt `data-id="${plantId}"`, damit `openHerbariumAtPlant()` sie via `querySelector` finden und highlighten kann.

**Panel-Höhe & Scroll:** `#panel-herbarium .panel-card` ist auf `max-height: 70vh` begrenzt und nutzt Flexbox-Column mit `overflow: hidden`. Titel und Tab-Leiste bleiben oben fixiert; die drei Tab-Content-Divs (`#herb-tab-welten`, `#herb-tab-familien`, `#herb-tab-trophies`) sind `overflow-y: auto` und scrollen eigenständig. Alle anderen Panel-Karten sind davon unberührt.

**Karten-Raster (`.herb-grid`) — horizontal scrollend:**
Statt einer wachsenden Vertikalliste zeigt jede Weltgruppe und jede Familiengruppe maximal **2 Reihen** Karten. Gibt es mehr Karten als in die sichtbare Breite passen, scrollt das Raster nach rechts.

```css
.herb-grid {
  display: grid;
  grid-template-rows: repeat(2, auto);  /* exakt 2 Reihen */
  grid-auto-flow: column;               /* Karten füllen erst Reihen, dann Spalten */
  grid-auto-columns: 124px;            /* feste Kartenbreite */
  overflow-x: auto;
  overflow-y: hidden;
}
```
Scrollbar: 4 px, dezent beige-braun (`scrollbar-width: thin`).

**Design:** Einheitliches Beige (`var(--beige-card)`), braune Randlinie. Pflanzenbild: `.herb-plant-img { width:78%; height:auto; max-width:90px }`.

**Interaktiver Pflanzensteckbrief (Pop-up):**
- Foto-Galerie (‹ ›, **4 Slides**, 4 Punkte-Navigation, `galleryIndex` wird beim Öffnen auf 0 zurückgesetzt):

| Index | Inhalt | Visuell |
|-------|--------|---------|
| 0 | **Pflanzenmodell** — `p.plantImg` clean | kein Extra-Filter, `scale(1.0)`, Label „Pflanzenmodell" |
| 1 | Eigenes Foto – Blütenaufnahme | `scale(1.18) translateY(-6%)` + `.gallery-img--photo` |
| 2 | Eigenes Foto – Blattaufnahme | `scale(0.92) rotate(-4deg)` + `.gallery-img--photo` |
| 3 | Eigenes Foto – Gesamtaufnahme | `scale(1.0)` + `.gallery-img--photo` |

`.gallery-img--photo` = `saturate(1.22) contrast(1.07) brightness(0.96)` + weißer Outline-Rahmen (Snapshot-Look). `GALLERY_TOTAL = 4` steuert den Modulo in `galleryMove()` und `quizPhotoNav()`. Die Quiz-Foto-Kachel verwendet dieselbe 4-Slide-Struktur (Modell + `galleryPhotos[0–2]` für Foto-Transforms).
- Steckbrief: Name, lateinischer Name, Familie, Blütezeit, Habitat, Fun Facts

---

## 11. Panel: „To-Do Liste"
Suchaufträge, Pflanzenaufträge und Fortbildungsaufträge in Kartenform mit XP-Anzeige.

---

## 11. Spielmechanik & XP-System

- **Scannen – 3-Fotos-Sequenz:** 3 Shutter-Klicks (Blüte → Blätter → Gesamtpflanze). Fortschritts-Dots (◯◯◯ → ●◯◯ → ●●◯ → ●●●). Erst beim 3. Klick landet die Pflanze im Körbchen.
- **Kamerasucher-Design:** Vollständig beige/cozy. Kein schwarzer UI-Stil. Kein Scanner-Balken — nach jedem Klick erscheint kurz „✓ Bild gespeichert", danach der nächste Hinweis.
- **Hinweistexte (zentriert im Sucher):** „Mache ein Bild von der Blüte" → „Mache ein Bild von den Blättern" → „Fotografiere die ganze Pflanze". Hinterlegter Beige-Chip, gut lesbar auf der Karte.
- **Bestimmen:** Bestimmungsschlüssel → Herbarium → XP.
- **Level-System:** XP → Level → neue Ausrüstungs-Items.
- **Badge oben rechts:** `Level X · Y/Z 🌿`

---

## 12. Pflanzenpins auf der Karte

Pins erscheinen **ausschließlich** für Pflanzen, die bereits vollständig bestimmt und in `herbarium[]` eingetragen sind. `updatePinPositions()` rechnet SVG-Koordinaten (ViewBox 460 × 820) auf CSS-Pixel um.

**Darstellung:** Nur das Pflanzen-PNG steht auf der Karte — kein Name, kein Häkchen. Wer die Pflanze kennenlernen möchte, tippt auf das Bild → Mini-Karte öffnet sich.

**Bodenschatten:** Kontaktschatten (kein Elevation-Effekt). `.pin-ground-shadow` liegt mit `margin-top: -10px` direkt unter der Pflanzenbasis; das Pflanzenbild selbst hat nur einen minimalen Ambient-Drop-Shadow (`0 0 3px`), der kein Schweben suggeriert.

**Aktuelle `pinData[]`:**

| ID | Name | SVG-Koordinaten | Besonderheit |
|----|------|----------------|--------------|
| `gaenseblumchen` | Gänseblümchen | 210 / 440 | — |
| `rotklee` | Wiesen-Rotklee | 400 / 455 | — |
| `lupine` | Vielblättrige Lupine | 230 / 560 | — |
| `kirschbaum` | Vogelkirsche | 350 / 245 | `large: true` → `.pin-plant-img--lg` (120 px statt 72 px) |
| `gefleckte_taubnessel` | Gefleckte Taubnessel | 148 / 298 | — |
| `goldnessel` | Goldnessel | 180 / 470 | — |
| `weisse_taubnessel` | Weiße Taubnessel | 350 / 500 | — |
| `hahnenfuss` | Kriechender Hahnenfuß | 120 / 530 | — |
| `linde` | Sommerlinde | 310 / 350 | `large: true` |

**scanPool** (Pflanzen die per Kamera gescannt werden können): `gaenseblumchen`, `lupine`, `gefleckte_taubnessel`, `goldnessel`, `weisse_taubnessel`, `hahnenfuss`, `linde`. Die Vogelkirsche (Frühblüher) ist bewusst ausgeschlossen.

**Größen-Varianten:** `.pin-plant-img` = 72 px Breite (Standard). `.pin-plant-img--lg` = 120 px (Bäume / große Pflanzen). Gesteuert durch `large: true` im `pinData`-Eintrag → `renderPins()` setzt die Klasse dynamisch.

---

## 13. Design & Technische Vorgaben
- **Vibe:** Cozy Game, Nintendo-Stil – Salbei-Grün, warme Cremetöne, abgerundete Ecken.
- **Farb-Variablen:**
  - `--cream: #f5f0e5` · `--beige-card: #ede8dc` · `--ink: #3d2b1f`
  - `--muted: #7a6552` · `--brown-border: #8B6848`
  - `--green-dark: #2e7d32` · `--sage: #c0d1ba`
- **Icon-System:** Lucide nur für dekorative Inline-Icons; alle Haupt-Buttons nutzen PNG-Assets.
- **Keine Emojis in Pflanzendarstellungen:** Herbarium, Basket, Quiz, Popup, Karten-Pin.
- **Trophäen-Assets (`assets/Trophies/`):**
  | Item | `trophyImg` | Unlock |
  |------|------------|--------|
  | Gärtnerschürze | `apron.png` ✅ | Level 2 |
  | Frühjahrspionier-Abzeichen | `pinbadge.png` ✅ | Alle Frühblüher |
  | Entdeckerhut | `hat.png` ✅ | Level 1 |
  | Goldene Lupe | `Lupe.png` ✅ | Level 5 |
  | Botanisches Tagebuch | — (Lucide) | 10 Pflanzen |
  | Fleißige Biene | `insects/Bee.png` ✅ | 3 Pflanzen im Herbarium |
- **Bestäuber-Assets (`assets/Trophies/insects/`):** PNGs von Insekten die als animierte Bestäuber über den Homescreen fliegen. Nur dieser Unterordner enthält fliegende Trophäen.
- **Technologie:** Einzelne HTML-Datei. Läuft direkt im Browser ohne Installation.
- **Desktop-Darstellung:** Die App wird im Browser als zentrierter 390 × 844 px großer Block (iPhone-Maße) dargestellt. Links und rechts erscheint ein dunkelgrauer Hintergrund (`#3a3a3a`). Umgesetzt via `#phone-shell`-Wrapper mit `transform: translateZ(0)` (damit `position:fixed`-Elemente innerhalb des Containers bleiben).
- **Dateinamen ohne Umlaute:** Alle Asset-Ordner und Dateien wurden umbenannt (z. B. `Frühblüher` → `Fruehblueher`, `Weiße_Taubnessel` → `Weisse_Taubnessel`), um Kompatibilitätsprobleme auf Windows zu vermeiden.

---

## 14. Welten-Planeten-System

3D-Kugel-Visualisierung des Sammelfortschritts. Zugänglich über den Sommer-Badge oben rechts.

### Trigger
`.top-badge-season` → `openWeltenOverlay()`.

### 3D-Kugel (aktive Welt: Sommerblüher)
- `.welten-orbit-ring` (220 × 220 px), rotiert via `weltenPlanetSpin` (120 s)
- `.welten-planet` (180 × 180 px): `radial-gradient` für Tiefeneffekt + `::after`-Pseudo-Element als Glanzpunkt oben-links
- `.welten-planet-core` (`assets/icons/sun.png`, 52 px): gegendrehend via `weltenCoreStay` (60 s) — bleibt immer aufrecht
- `.welten-orbit-plant`: Pflanzen rotieren **mit** der Kugel (keine Gegenrotation). Jede Pflanze bekommt bei der Platzierung eine initiale `transform: rotate(angleDeg + 90deg)`, sodass ihre Basis zum Kugelzentrum zeigt. Manche Pflanzen stehen beim Rotieren auf dem Kopf — gewollter Effekt. Positionen und Größen variieren je Slot (34–48 px, Radien 50–85 px).

### Inaktive Welten
`.welten-mini-planet` (62 × 62 px, `opacity:0.60`, `grayscale(0.25)`). Klick → `showSeasonInfo(id)` → Saisoninfo-Popup.

### `worldDefs[]` Datenstruktur
`{ id, name, iconImg, bg, miniColor, plants, active }`

### Farben der Welten
| Welt | Icon | Gradient |
|------|------|----------|
| Sommerblüher (aktiv) | `sun.png` | Mint-Grün `#d4eea0 → #8dc85a → #2e6a1e` |
| Frühblüher | `Fruehblueher.png` | Warm-Gelb `#f5ee88 → #d4b820 → #6a5008` |
| Herbstblüher | `Herbstblueher.png` | Terrakotta `#f0c07a → #d07030 → #5a1e06` |
| Winterblüher | `Winterblueher.png` | Stahlblau `#b8d8f0 → #6898c8 → #102040` |

---

## 15. Bestäuber-System

Animierte Insekten fliegen über den Homescreen — freigeschaltet als Trophäen, sobald bestimmte Bedingungen erfüllt sind.

### Konzept
Bestäuber erscheinen **nur auf dem Homescreen** (nicht bei offenen Panels). Sie fliegen in drei zufälligen Verhaltensweisen:

| Modus | Beschreibung |
|-------|-------------|
| **Pflanze → Pflanze** | Fliegt von einem Kartenpin zum nächsten (realistisches Bestäuberverhalten) |
| **Kreisbahn** | Dreht eine Runde über der Karte und verschwindet wieder |
| **Drift** | Gleitet auf einer leicht geschwungenen Bézier-Kurve von einer Seite zur anderen |

### Technische Umsetzung
- **`pollinatorDefs[]`** — definiert jeden Bestäuber: `id`, `img`, `size`, `isUnlocked()`, `interval` (ms zwischen Auftritten), `speed` (ms pro Flug)
- **`initPollinators()`** — wird in `init()` aufgerufen; startet Timer für alle bereits freigeschalteten Bestäuber
- **`_schedulePollinatorFlight(def)`** — plant den nächsten Auftritt mit zufälligem Jitter
- **`_launchPollinatorFlight(def)`** — wählt Modus, erstellt `<img>`-Element, animiert per `requestAnimationFrame` / CSS-Transition, entfernt Element danach
- Ist ein Panel offen (`activePanel !== null`), wird der Flug übersprungen und neu geplant
- In `completeIdentification()` werden neu freigeschaltete Bestäuber sofort gestartet

### Aktuell implementierte Bestäuber
| ID | Asset | Unlock-Bedingung |
|----|-------|-----------------|
| `bee` | `assets/Trophies/insects/Bee.png` | 3 Pflanzen im Herbarium |
