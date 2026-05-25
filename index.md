# Prompt: TC 1928 Ochtrup – Dynamischer Spielplan

## Aufgabe

Passe eine bestehende statische HTML-Datei (Spielplan 2026 des TC 1928 Ochtrup) so an, dass sie **dynamisch** wird und live Daten von liga.nu lädt. Die Seite soll als einzelne HTML-Datei funktionieren und auf GitHub Pages deploybar sein.

---

## Anforderungen

### Datenbezug
- Live-Daten von **20 liga.nu-URLs** laden (Spielstände, Termine, Uhrzeiten, verlegte Spiele)
- CORS-Einschränkungen umgehen via öffentliche Proxy-Dienste (allorigins.win, corsproxy.io)
- Mannschaften, die **zurückgezogen** haben, sollen komplett ausgeblendet werden

### Anzeige
- **„Nächstes Spiel"-Box** oben mit Datum und Countdown in Tagen
  - Countdown bezieht sich nur auf die per Button aktiv gefilterten Mannschaften (alle vier Filter)
  - Label und Datum/Uhrzeit-Zeile gut lesbar (Größe und Helligkeit), Team-Zeile etwas größer
- **Spielkarten** mit Datum, Uhrzeit, Heim-/Auswärtsteam, Ergebnis, Mannschafts-Tag und „Tabelle"-Button
  - Der „Tabelle"-Button sitzt direkt unter dem Mannschafts-Tag (rechtsbündig)
  - Klick auf Karte **mit Ergebnis** → lädt und zeigt den Spielbericht innerhalb der App
  - Klick auf „Tabelle"-Button → zeigt die Ligatabelle der jeweiligen Mannschaft

### Spielbericht-Ansicht
- Spielbericht wird per Proxy von liga.nu geladen und im App-Design dargestellt
- Enthält: Match-Header (Teams, Ergebnis, Datum/Uhrzeit), Einzel- und Doppel-Tabellen
- liga.nu-Besonderheit: Einzelspiele und Doppelspiele stehen in **einer gemeinsamen Tabelle**; einzeilige Zellen mit Text „Einzelspiele" / „Doppelspiele" sind Abschnitts-Trenner und werden als Überschriften gerendert
- **Einzelspiele**: Spalten Seed · Name · Gegnerseed · Gegnername · Sätze…; LK-Info (in Klammern) steht in neuer Zeile unter dem Namen (`<br>` vor „(")
- **Doppelspiele**: Liga.nu liefert eine kombinierte Zeile mit 12 Zellen (homeSeeds · 3 · homeNames · awaySeeds · 3 · awayNames · Sätze…); die „3"-Summen-Zellen (Position 1 und 4) werden entfernt; Spielernamen mit Rangklammer-Suffix `(Zahl)` werden bereinigt; pro Spielerpaar eine eigene Zeile, Ergebniszellen nur in der ersten Zeile; Team-Header bekommen `colspan=3`, Datenzeilen enthalten je zwei Leerzellen als Platzhalter → 12 Spalten insgesamt für korrekte Ausrichtung
- Spielernamen immer **linksbündig** (inline `style="text-align:left"` auf Namens-Zellen in Doppelzeilen)
- Satz-Ergebnisse werden eingefärbt: grün = gewonnen, rot = verloren
- Buttons: „← Zurück" (zur Spielliste) und „Tabelle" (zur Ligatabelle)

### Tabellen-Ansicht
- Ligatabelle wird aus dem bereits gecachten HTML geparst (kein zusätzlicher Request)
- Spalten: Rang, Mannschaft, Begegn., S, U, N, Punkte, Matches, Sätze, Games
- Ochtrup-Zeile wird hervorgehoben
- Button: „← Zurück" (zur Spielliste)

### Filter & Steuerung
- **Buttonbereich (drei Zeilen):**
  - Zeile 1: Vergangen · Aktuell · Heim · Auswärts (unabhängige Toggle-Filter)
  - Zeile 2: „Mannschaften"-Button (volle Breite) — klappt Zeile 3 ein/aus
  - Zeile 3 (ausklappbar): ganz oben „Alle auswählen" (grün) und „Keine auswählen" (rot), dann je ein Button pro Mannschaft + Neu-laden-Button (↺)
  - „Alle auswählen" aktiviert alle 18 Mannschafts-Buttons; „Keine auswählen" deaktiviert alle — beide speichern den Zustand und rendern sofort neu
- Standardzustand beim Start: Aktuell, Heim, Auswärts aktiv — Vergangen inaktiv, Mannschaftsauswahl eingeklappt
- Aktive Buttons: weiße fette Schrift; Rand und Hintergrund in Mannschaftsfarbe (Zeile 3) bzw. Grau (Zeile 1/2)
- Button-Konstellation wird im Browser gespeichert (`localStorage`) und beim nächsten Aufruf wiederhergestellt
- Zwischen letztem vergangenen und erstem kommenden Spiel: größerer visueller Abstand
- **Mannschaftsauswahl Auto-Close**: Öffnen startet 10-Sekunden-Timer; jeder Button-Klick in Zeile 3 startet Timer neu auf 5 Sekunden; manuelles Schließen bricht Timer ab
- **Klick auf Mannschafts-Button** führt immer zur Spiellisten-Ansicht zurück (kein Verbleib auf Spielbericht- oder Tabellen-Ansicht)

### Mannschaften (20 Ligen)
| Button-Label | team-ID | liga.nu group |
|---|---|---|
| Junioren U15 classic Kreisklasse 1 | classic | 249 |
| Junioren U15 2er Kreisklasse 1 | 2er | 237 |
| Mixed U15 2er Kreisliga | mixed | 194 |
| Gemischt U8 Kleinfeld Kreisliga | u8 | 201 |
| Gemischt U10 Grossfeld Kreisklasse 1 | u10 | 222 |
| Damen 4er Kreisliga | d4 | 7 |
| Damen 30 4er Bezirksliga | d30bz | 18 |
| Damen 30 4er Kreisliga | d30kl | 22 |
| Damen 40 4er Bezirksliga | d40 | 32 |
| Damen 50 4er Bezirksliga | d50 | 45 |
| Damen 60 Doppel Bezirksliga | d60 | 57 |
| Herren 4er Kreisklasse 1 | h4 | 65 |
| Herren 30 4er Kreisliga | h30kl | 86 |
| Herren 30 4er Kreisklasse 3 | h30k3 | 89 |
| Herren 40 4er Bezirksklasse | h40bk | 105 |
| Herren 40 4er Kreisklasse 2 | h40kl | 109 |
| Herren 50 4er Kreisklasse 1 | h50 | 128 |
| Herren 60 4er Bezirksliga | h60 | 143 |
| Herren 65 4er Kreisklasse 1 | h65kl | 154 |
| Herren 65 Doppel Münsterlandliga | h65ml | 158 |

Basis-URL: `https://wtv.liga.nu/cgi-bin/WebObjects/nuLigaTENDE.woa/wa/groupPage?targetFed=WTV&championship=MS+2026&group=`

### Performance
- Alle **20** Ligen **parallel** laden
- Proxies pro Request **gleichzeitig** ansteuern (`Promise.any()`), schnellster gewinnt, übrige werden abgebrochen
- **Progressive Darstellung**: Jede Liga wird sofort angezeigt, sobald sie geladen ist
- Timeout pro Proxy-Versuch: 10 Sekunden

### Design
- Dunkles Theme (`#0f1117` Hintergrund)
- Schriften: Bebas Neue (Überschrift, „Tabelle"-Button), DM Sans (Fließtext)
- Jede Mannschaft hat eine eigene Farbe (Karte, Tag, Button)
- **Heimspiele**: weißer Kartenrand
- Ochtrup-Team in Karte weiß und fett, Gegner-Team blasser
- Liga-Tag in Karten weiß und fett
- Vergangene Spiele gedimmt (opacity 0.55)
- Mobiloptimiert (max-width 640px)

---

## Technische Rahmenbedingungen

- **Einzelne HTML-Datei** — kein Build-Prozess, kein Backend
- Deployment auf **GitHub Pages** (`index.html` im Root)
- CORS-Proxies: allorigins /get, corsproxy.io, allorigins /raw — alle drei gleichzeitig (`Promise.any()`)
- liga.nu-Besonderheit: Datum steht in Kopfzeilen, gespielte Matches haben leere Datumszellen → Datum wird zeilenübergreifend weitergegeben (carry-forward)
- Gespielte Matches enthalten einen Spielbericht-Link (`meetingReport`), der beim Parsen extrahiert und im Match-Objekt gespeichert wird
- Spielbericht-Tabelle: Einzel- und Doppelspiele befinden sich in **einer gemeinsamen Tabelle**; Abschnitts-Trenner sind einzeilige Zellen mit exakt dem Text „Einzelspiele" bzw. „Doppelspiele"
- Doppelspiele: kombinierte Zeile hat 12 Zellen (homeSeeds · summe · homeNames · awaySeeds · summe · awayNames · 6×Score); Summenzellen an Index 1 und 4 entfernen; Header-Teamnamen auf `colspan=3` setzen; pro Paar eine Datenzeile mit zwei Leerzellen als Platzhalter → stets 12 Spalten für korrekte Ausrichtung
