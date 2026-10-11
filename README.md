# AT Zombies 🧟

Rundenbasiertes Zombie-Survival in Ego-Perspektive – optional mit Anmeldung über das
AT Protocol (Bluesky). Highscores werden dann als eigene Records **im Daten-Repo des
Spielers** gespeichert. Kein Backend, keine eigene Datenbank.

## Spielen

GitHub Pages aktivieren (einmalig):

1. Repo öffnen → **Settings → Pages**
2. Source: **Deploy from a branch** · Branch: **main** · Ordner: **/(root)** → **Save**
3. Danach erreichbar unter <https://falksan.github.io/at-zombies/>

## Spielprinzip

Du stehst hinter deiner Barrikade am Ende einer Straße. Pro Runde hast du **2 Aktionen**,
danach ziehen die Zombies weiter. Reine Tipp-Steuerung – läuft auf Handy und Desktop.

| Aktion | Effekt |
|---|---|
| 🎯 Schießen | 80 % Trefferchance auf den vordersten Zombie (40 Schaden), 20 % Kopftreffer (100 Schaden) |
| 🔄 Nachladen | Magazin (6 Schuss) aus der Reserve füllen (Start: 24 Schuss) |
| 🔧 Reparieren | Barrikade +25 (max. 100) |
| 💊 Verbandskasten | +1 Leben (Start: 2 Kits, max. 3 Leben) |
| 🔎 Spähen | 40 % +8 Munition · 25 % Verbandskasten · 35 % nichts |

**Zombies:** Walker (40 HP, langsam) · Läufer (25 HP, rückt doppelt so schnell vor, ab Runde 4) ·
Bruter (100 HP, 25 Barrikaden-Schaden, ab Runde 6). Zombies an der Barrikade greifen sie an;
ist sie gefallen, trifft dich pro Runde 1 Treffer.

**Punkte:** überstandene Runde +10 · Walker +25 · Läufer +35 · Bruter +50

## Wie die ATproto-Anbindung funktioniert

- Login komplett im Browser über `@atproto/oauth-client-browser` (OAuth mit PKCE + DPoP,
  Tokens liegen in der IndexedDB des Browsers – kein Backend, keine App-Passwords).
- Die App identifiziert sich über die gehostete `client-metadata.json` (dynamische
  Client-Registrierung; `client_id` muss exakt der gehosteten URL entsprechen).
- Nach dem Login werden Highscores als Records vom Typ `dev.atzombies.score` (Felder:
  `score`, `round`, `kills`) per `com.atproto.repo.putRecord` direkt ins Repo des
  Spielers auf seinem PDS geschrieben – die Daten gehören damit dem Spieler, nicht diesem Spiel.
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
