# Spritverbrauch

Persönliche Web-App (PWA) zum Erfassen von Tankvorgängen und Berechnen des Verbrauchs. Läuft auf dem iPhone als App auf dem Home-Bildschirm. Fahrzeug: Golf 8 GTI, 50 l Tank.

Antworten, UI-Texte und Code-Kommentare auf Deutsch.

## Aufbau

Kein Build-Schritt, keine Abhängigkeiten, kein Framework. Was im Repo liegt, wird so ausgeliefert.

- `index.html` – die ganze App: CSS, HTML und JavaScript inline (ca. 1250 Zeilen). Abschnitte im Skript sind mit `/* ===== … ===== */` markiert: Datenhaltung, Grundwerte & Formate, Berechnung, Zustand, Darstellung, Diagramm, Eingabe, Sichern & Laden, Start.
- `sw.js` – Service Worker für den Offline-Betrieb (network-first, nach 3,5 s Fallback auf den Cache).
- `manifest.webmanifest`, `icon-*.png` – PWA-Metadaten und Icons.

## Veröffentlichung

GitHub Pages: https://mason1-glitch.github.io/spritverbrauch/ (Repo `mason1-glitch/spritverbrauch`). Was auf `main` landet, ist live. Änderungen deshalb auf einem eigenen Branch machen und erst nach dem Testen nach `main` bringen.

## Lokal testen

`python3 -m http.server 8080` im Repo-Ordner, dann http://localhost:8080 (in `.claude/launch.json` als `spritverbrauch` hinterlegt). Auf localhost registriert sich der Service Worker ebenfalls.

## Worauf zu achten ist

- **Daten liegen nur auf dem iPhone** in `localStorage` (`spritverbrauch.v1` = Einträge und Einstellungen, `spritverbrauch.meta` = letzte Sicherung, `sv-period` = gewählter Zeitraum). Das Format eines Eintrags (`{id, date, km, liters, pricePerLiter, total, full, createdAt, updatedAt}`) nie inkompatibel ändern. Neue Felder müssen optional sein, `clean()` muss alte Einträge und alte Sicherungen weiter einlesen können.
- **Sicherungsformat** `{app: 'spritverbrauch', version: 1, exportedAt, settings, entries}` muss importierbar bleiben.
- **Datenzugriff nur über das Store-Interface** (`LocalStore`, später `HttpStore` über `API_BASE`), siehe Kommentar am Anfang des Skripts.
- **Verbrauch nach Volltank-Methode** (`analyse()`): Teilbetankungen zählen zur nächsten Volltankung.
- **Service Worker**: Kommt eine neue Datei zur App hinzu, gehört sie in `SHELL` in `sw.js`, und `CACHE` bekommt eine neue Versionsnummer.
- **Gestaltung**: Farben nur über die Variablen in `:root` (mit Dark-Mode-Block), Zahlen über `fmt`/`nf` im deutschen Format. Bedienung ist für das iPhone gedacht: Safe-Areas, Touch-Ziele ab 44 px, `inputmode="decimal"`.
