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
| 🧟 Walker | 1 Treffer, +25 Punkte (gemischte Zombie-Emojis 🧟 🧟‍♀️ 🧟‍♂️) |
| 👻 Läufer | 1 Treffer, +35 Punkte, zappelt in der Reihe |
| 🎃 Bruter | 3 Treffer, +50 Punkte |
| Gefahrenzone (rote Linie) | Jeder Zombie, der sie erreicht, ist durchgebrochen und kostet 1 Leben |
| Fundstücke | ↔ breitere Barrikade · ● Multi-Ball · ❤ Extra-Leben · 💀 die Horde rückt sofort vor |

Die Horde kommt wie ein Filmabspann: als Endlos-Strom, der kontinuierlich von oben
hereinrollt – Reihen mit mal mehr, mal weniger Zombies, oft als enges
Grüppchen nebeneinander, mit Lücken dazwischen, kein
Nachrücken pro Kill, kein Block-Reset. Mit der Zeit wird alles schwerer: Der Strom
läuft schneller und dichter (mehr Zombies pro Reihe, ab Welle 2 Läufer, ab Welle 3
Bruter), und alle 40 Sekunden steigt die „Welle“. Reine Wisch-/Tipp-Steuerung, Spielfeld
im Hochkant-Format – optimiert fürs Handy, läuft auch auf dem Desktop.

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
