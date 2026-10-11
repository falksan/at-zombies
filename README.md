# AT Zombies 🧟

**Breakout gegen eine Zombie-Horde**: Die Horde steht in Reihen und kriecht langsam
auf dich zu. Du lenkst die Barrikade am unteren Rand und wirfst eine Leuchtkugel ab,
die Zombies beim Auftreffen erledigt. Optional mit Anmeldung über das AT Protocol
(Bluesky) – Highscores werden als eigene Records **im Daten-Repo des Spielers**
gespeichert. Kein Backend, keine eigene Datenbank.

## Spielen

GitHub Pages aktivieren (einmalig):

1. Repo öffnen → **Settings → Pages**
2. Source: **Deploy from a branch** · Branch: **main** · Ordner: **/(root)** → **Save**
3. Danach erreichbar unter <https://falksan.github.io/at-zombies/>

## Spielprinzip

| Element | Bedeutung |
|---|---|
| ↔ Barrikade | Dein Paddle – Finger ziehen (oder Maus) bewegt sie |
| Leuchtkugel | Der Ball – Tippen/Klick wirft ihn ab, fällt er ins Leere: −1 Leben |
| Walker (grün) | 1 Treffer, +25 Punkte |
| Läufer (orange) | 1 Treffer, +35 Punkte, zappelt in der Reihe |
| Bruter (dunkel) | 3 Treffer, +50 Punkte |
| Gefahrenzone (rote Linie) | Erreicht die Horde sie, gibt es −1 Leben und die Horde weicht zurück |
| Fundstücke | ↔ breitere Barrikade · ● Multi-Ball · ❤ Extra-Leben · 💀 die Horde rückt sofort vor |

Horde komplett erledigt = +50 Punkte, nächste Welle (mehr Reihen, schnellere Horde,
schnellere Leuchtkugel). Reine Wisch-/Tipp-Steuerung – läuft auf Handy und Desktop.

## Wie die ATproto-Anbindung funktioniert

- Login komplett im Browser über `@atproto/oauth-client-browser` (OAuth mit PKCE + DPoP,
  Tokens liegen in der IndexedDB des Browsers – kein Backend, keine App-Passwords).
- Die App identifiziert sich über die gehostete `client-metadata.json` (dynamische
  Client-Registrierung; `client_id` muss exakt der gehosteten URL entsprechen).
- Nach dem Login werden Highscores als Records vom Typ `dev.atzombies.score` (Felder:
  `score`, `wave`, `kills`) per `com.atproto.repo.putRecord` direkt ins Repo des
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
