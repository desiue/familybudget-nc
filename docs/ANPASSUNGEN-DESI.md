# Eigene Anpassungen (desiue) gegenüber holger-dev/familybudget-nc

Basis: Upstream v0.4.4 (`cdedc7c`, 24.06.2026) = Store-Paket, das auf majkic.de installiert war.
Eigene Version: **0.4.4.1** (eindeutig, damit eine echte Upstream-0.4.5 weiterhin als Update erkannt wird).

## Geänderte Dateien

| Datei | Änderung |
|---|---|
| `src/components/expenses/ExpenseList.vue` | Monatskopf zeigt „Monat: …“ und zusätzlich eine zweite Zeile mit **laufender Gesamtsumme** und **laufendem Ausgleich** (kumuliert über alle Monate bis einschließlich des jeweiligen Monats). Handy-Layout < 480 px. |
| `src/components/expenses/ExpenseFilters.vue` | Benutzerfilter mit `v-model`, Label/Platzhalter, Dropdown `append-to-body`; Filterfelder in eigenen Containern (`filter-item`), Zurücksetzen-Button in eigenem Container; einspaltig < 480 px. |
| `appinfo/info.xml` | `version` 0.4.4 → 0.4.4.1, `max-version` 33 → 34 (läuft auf Nextcloud 34). |
| `js/familybudget.js`, `css/familybudget.css` | Build-Ergebnis (nicht in Git, siehe `.gitignore`) – liegt im Release-Archiv. |

## Nachweis

Am 22.10.2026 aus diesem Stand mit `npm ci && npm run build` (Node 22) gebaut:
`js/familybudget.js`, `css/familybudget.css` und `js/familybudget.js.LICENSE.txt` sind **bitgleich** (sha256) mit den Dateien auf dem Produktivsystem.

## Nach einem Store-Update

Ein Update aus dem App-Store ersetzt den kompletten App-Ordner – die Anpassungen sind dann weg.
1. Upstream-Änderungen in diesen Fork mergen (`git fetch upstream && git merge upstream/main`).
2. `npm ci && npm run build`, Version auf `<upstream>.1` setzen, Release-Archiv bauen.
3. Archiv nach `apps/familybudget` auf dem Server entpacken, `php occ app:list | grep familybudget` prüfen.
