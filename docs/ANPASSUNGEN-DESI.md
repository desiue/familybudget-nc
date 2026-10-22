# Eigene Anpassungen (desiue) gegenüber holger-dev/familybudget-nc

Basis: Upstream v0.4.4 (`cdedc7c`, 24.06.2026) = Store-Paket, das auf majkic.de installiert war.
Eigene Version: **0.4.5.4** (0.4.5.1 = Übernahme Produktivstand, 0.4.5.2 = Layout-Korrektur, 0.4.5.3 = Benutzerfeld wie Textfelder, 0.4.5.4 = iOS-Datumsfelder).

Warum nicht 0.4.4.1? Auf dem Server war die `info.xml` schon von Hand auf 0.4.5 gesetzt, Nextcloud hat 0.4.5 als installierte
Version gespeichert. Getestet am 22.10.2026: Eine niedrigere Nummer (0.4.4.1) setzt die ganze Instanz auf
`needsDbUpgrade: true` (Upgrade-Seite für alle Benutzer) – zurückgerollt. Die eigene Nummer muss daher immer **höher**
als die installierte sein; beim ersten Einspielen ist einmal `php occ upgrade` nötig.

## Geänderte Dateien

| Datei | Änderung |
|---|---|
| `src/components/expenses/ExpenseList.vue` | Monatskopf zeigt „Monat: …“ und zusätzlich eine zweite Zeile mit **laufender Gesamtsumme** und **laufendem Ausgleich** (kumuliert über alle Monate bis einschließlich des jeweiligen Monats). Handy-Layout < 480 px. |
| `src/components/expenses/ExpenseFilters.vue` | Benutzerfilter mit `v-model`, Label/Platzhalter, Dropdown `append-to-body`; Filterfelder in eigenen Containern (`filter-item`), Zurücksetzen-Button in eigenem Container; einspaltig < 480 px. |
| `appinfo/info.xml` | `version` 0.4.4 → 0.4.5.4, `max-version` 33 → 34 (läuft auf Nextcloud 34). |
| `js/familybudget.js`, `css/familybudget.css` | Build-Ergebnis (nicht in Git, siehe `.gitignore`) – liegt im Release-Archiv. |

## Layout-Korrektur 0.4.5.2 (22.10.2026)

| Problem | Ursache | Korrektur |
|---|---|---|
| Filterleiste breiter als Titel/Liste, „Zurücksetzen“ abgeschnitten (PC) | `max-width: 900px; margin: auto` des Originals fehlte, starres 5-Spalten-Raster | Flex-Leiste mit Umbruch, max. 900 px zentriert |
| Benutzerfeld überlappt „Von“ | Mindestbreite von NcSelect | `min-width: 0` |
| iPhone: Seite seitlich verschiebbar | Eingabefelder `content-box` + Innenabstand → ragen ~20 px über | `box-sizing: border-box` |
| iPhone: Datumsfelder unsichtbar | leeres `input[type=date]` klappt in iOS Safari zusammen | `min-height`, `appearance: none` |
| iPhone (0.4.5.3): Von/Bis ragen über den Rand, leeres Feld ohne Rahmen | iOS Safari: Datumsfeld mit eigener Mindestbreite, leerer Wertbereich Höhe 0 | `min-width: 0`, `::-webkit-date-and-time-value { min-height }`, `overflow-x: clip` als Sicherheitsnetz (0.4.5.4) |
| Labels uneinheitlich | eigene Regel zwang alle Labels auf `display:block; height:20px` | Regel entfernt, Benutzerfeld nur Platzhalter + aria-label |
| Benutzerfeld anders als Suchfeld (0.4.5.3) | NcSelect nutzt noch 2-px-Rahmen, Textfelder den neuen Stil (Linie oben + blasser Ring), 4 px Außenabstand unten | Rahmen per Schatten wie Textfeld, gleiche Rundung/Höhe/Unterkante |
| „Gesamt“ in derselben Zeile wie „Monat“ | Monatskopf war Flex-Zeile ohne Umbruch | Raster: „Gesamt“ immer eigene Zeile |

Gemessen im Testaufbau (Chromium, 1280 px und 390 px): Filterleiste bündig mit Liste (182–1098 px), keine Überbreite.

## Nachweis

0.4.5.1: Am 22.10.2026 aus diesem Stand mit `npm ci && npm run build` (Node 22) gebaut:
`js/familybudget.js`, `css/familybudget.css` und `js/familybudget.js.LICENSE.txt` sind **bitgleich** (sha256) mit den Dateien auf dem Produktivsystem.

## Nach einem Store-Update

Ein Update aus dem App-Store ersetzt den kompletten App-Ordner – die Anpassungen sind dann weg.
1. Upstream-Änderungen in diesen Fork mergen (`git fetch upstream && git merge upstream/main`).
2. `npm ci && npm run build`, Version auf `<upstream>.1` setzen, Release-Archiv bauen.
3. Archiv nach `apps/familybudget` auf dem Server entpacken, `php occ app:list | grep familybudget` prüfen.
