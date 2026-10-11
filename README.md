# AT Zombies 🧟

Survival-Browserspiel: Zombies Wellen für Wellen überleben – optional mit Anmeldung über das
AT Protocol (Bluesky). Highscores werden dann als eigene Records **im Daten-Repo des Spielers**
gespeichert. Kein Backend, keine eigene Datenbank.

## Spielen

GitHub Pages aktivieren (einmalig):

1. Repo öffnen → **Settings → Pages**
2. Source: **Deploy from a branch** · Branch: **main** · Ordner: **/(root)** → **Save**
3. Danach erreichbar unter <https://falksan.github.io/at-zombies/>

## Steuerung

- **Desktop:** WASD/Pfeiltasten = laufen · Maus = zielen · Klick (halten) = schießen
- **Handy:** Daumen ziehen = laufen · Zielen & Feuern automatisch

Punkte: Kill 10 (Bruter 30), geschaffte Welle +50. Ab Welle 3 tauchen Bruter auf
(langsamer, halten 3 Treffer aus). Alle 3 Leben weg = Game Over.

## Wie die ATproto-Anbindung funktioniert

- Login komplett im Browser über `@atproto/oauth-client-browser` (OAuth mit PKCE + DPoP,
  Tokens liegen in der IndexedDB des Browsers – kein Backend, keine App-Passwords).
- Die App identifiziert sich über die gehostete `client-metadata.json` (dynamische
  Client-Registrierung; `client_id` muss exakt der gehosteten URL entsprechen).
- Nach dem Login werden Highscores als Records vom Typ `dev.atzombies.score` per
  `com.atproto.repo.putRecord` direkt ins Repo des Spielers auf seinem PDS geschrieben –
  die Daten gehören damit dem Spieler, nicht diesem Spiel.
- Gespeicherte Scores werden über `com.atproto.repo.listRecords` gelesen und angezeigt.

### Eigenes PDS betreiben?

In der `index.html` oben im `CONFIG`-Block den `handleResolver` auf die eigene PDS-URL
ändern – dann geht die Handle-Auflösung (und damit IP + Handle beim Login) nicht an Bluesky.

### Andere Domain?

`client_id`, `client_uri` und `redirect_uris` in der `client-metadata.json` anpassen.

### Lokal entwickeln

    python3 -m http.server 8080
    # dann http://127.0.0.1:8080 öffnen

Loopback-OAuth läuft ohne `client-metadata.json` (Refresh-Tokens sind dann bewusst nur
ca. 1 Tag gültig).
