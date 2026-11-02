# Modul 2.6: Capstone – Server-Übergabe

## Anschluss an Modul 2.5

In den Modulen 2.1 bis 2.5 hast du dich im Terminal orientiert, Dateien
angelegt, gelesen, bearbeitet, gesucht und Ausgaben in Dateien gesammelt.
Jetzt verbindest du diese Fähigkeiten in einem zusammenhängenden
Arbeitsauftrag.

## Szenario

Dein Kollege Kai hat das Unternehmen verlassen. Du übernimmst seinen
Übungsserver. Die Übergabe-Notizen sind vage, Dateien liegen verstreut und
die Projektstruktur fehlt. Finde, ordne, repariere und dokumentiere den
Zustand.

Du arbeitest nur mit selbst erzeugten Übungsdateien. Lege zuerst die
Übungsumgebung mit den Befehlen auf der nächsten Seite an.

> [!CAUTION]
> Schreibende und löschende Befehle bleiben in den hier genannten, selbst
> erzeugten Pfaden. Prüfe vor `mv`, `cp`, `nano`, `>` und `rm` immer den
> vollständigen Zielpfad. Verwende kein `sudo`.

<!-- pagebreak -->

```bash
mkdir -p ~/capstone-sandbox/fundstuecke/{config,docs,logs}
mkdir -p ~/ablage
printf 'Projekt Webshop\nConfig liegt verteilt.\n' > ~/uebergabe-notizen.txt
printf 'APP_PORT=8080\n' > ~/capstone-sandbox/fundstuecke/config/app.conf
printf '%s\n' \
  'DB_NAME=webshop_dev' \
  'DB_HOST=localhost' \
  > ~/capstone-sandbox/fundstuecke/config/db.conf
printf '%s\n' \
  '# Webshop' \
  'Uebernommenes Projekt ohne aktuelle Dokumentation.' \
  > ~/capstone-sandbox/fundstuecke/docs/README.md
printf '%s\n' \
  '08:01 GET / 200' \
  '08:02 GET /produkte 200' \
  '08:03 GET /login 200' \
  '08:04 GET /admin 401' \
  '08:05 GET /admin 401' \
  '08:06 GET /produkte 200' \
  '08:07 GET /warenkorb 200' \
  '08:08 GET /checkout 200' \
  '08:09 GET /checkout 500' \
  '08:10 GET /status 200' \
  '08:11 GET /produkte 200' \
  '08:12 GET /login 200' \
  '08:13 ERROR Datenbankverbindung fehlgeschlagen' \
  '08:14 GET /status 200' \
  '08:15 GET / 200' \
  '08:16 GET /produkte 200' \
  '08:17 GET /login 200' \
  '08:18 GET / 200' \
  '08:19 GET /status 200' \
  '08:20 GET / 200' \
  > ~/capstone-sandbox/fundstuecke/logs/access.log
printf 'Wichtige Information\n' > ~/ablage/wichtig.txt
printf 'Kontakte des Teams\n' > ~/ablage/kontakte.txt
printf '# Checkliste\n' > ~/ablage/checkliste.md
touch ~/ablage/alt.tmp ~/ablage/test.tmp
```

<!-- pagebreak -->

## 1. Überblick verschaffen

Lies zuerst die Übergabe-Notizen:

```bash
cat ~/uebergabe-notizen.txt
```

Erkunde dein Home-Verzeichnis einschließlich versteckter Dateien:

```bash
pwd
ls -la ~
```

Erstelle den Ist-Zustand in `~/status.txt`. Der Bericht muss mindestens deinen
Benutzernamen, den Hostnamen und eine Auflistung deines Home-Verzeichnisses
enthalten:

```bash
echo "=== Ist-Zustand ===" > ~/status.txt
echo "Benutzer:" >> ~/status.txt
whoami >> ~/status.txt
echo "Hostname:" >> ~/status.txt
hostname >> ~/status.txt
echo "Eintraege im Home-Verzeichnis:" >> ~/status.txt
ls -la ~ >> ~/status.txt
```

Prüfe den Bericht:

```bash
cat ~/status.txt
```

<!-- pagebreak -->

## 2. Projektstruktur anlegen

Erstelle unter `~/projekte/webshop/` die Verzeichnisse `src`, `config`,
`docs`, `logs` und `backups`. Markiere jedes zunächst leere Verzeichnis mit
`.gitkeep`:

```bash
mkdir -p ~/projekte/webshop/{src,config,docs,logs,backups}
touch ~/projekte/webshop/src/.gitkeep
touch ~/projekte/webshop/config/.gitkeep
touch ~/projekte/webshop/docs/.gitkeep
touch ~/projekte/webshop/logs/.gitkeep
touch ~/projekte/webshop/backups/.gitkeep
```

Prüfe die Struktur mit `tree`, falls vorhanden, sonst mit `find`:

```bash
if command -v tree >/dev/null 2>&1; then
    tree -a ~/projekte/webshop
else
    find ~/projekte/webshop -print
fi
```

## 3. Verstreute Dateien finden und einsammeln

Die Übungsdateien liegen unter `~/capstone-sandbox/fundstuecke`. Suche sie
mit `find`, bevor du sie verschiebst:

```bash
find ~/capstone-sandbox/fundstuecke -type f
find ~/capstone-sandbox/fundstuecke -type f -name "app.conf"
find ~/capstone-sandbox/fundstuecke -type f -name "db.conf"
find ~/capstone-sandbox/fundstuecke -type f -name "README.md"
find ~/capstone-sandbox/fundstuecke -type f -name "access.log"
```

Verschiebe die Dateien an ihre neuen Orte:

```bash
mv ~/capstone-sandbox/fundstuecke/config/app.conf ~/projekte/webshop/config/
mv ~/capstone-sandbox/fundstuecke/config/db.conf ~/projekte/webshop/config/
mv ~/capstone-sandbox/fundstuecke/docs/README.md ~/projekte/webshop/docs/
mv ~/capstone-sandbox/fundstuecke/logs/access.log ~/projekte/webshop/logs/
```

Prüfe die neuen Orte:

```bash
find ~/projekte/webshop -type f -print
```

<!-- pagebreak -->

## 4. Backup erstellen und Konfiguration anpassen

Lies die Datenbank-Konfiguration zuerst:

```bash
cat ~/projekte/webshop/config/db.conf
```

Erstelle eine Sicherungskopie:

```bash
cp ~/projekte/webshop/config/db.conf ~/projekte/webshop/config/db.conf.backup
```

Öffne anschließend die Originaldatei mit `nano`:

```bash
nano ~/projekte/webshop/config/db.conf
```

Ändere nur `DB_NAME=webshop_dev` zu `DB_NAME=webshop_prod`. Speichere mit
`Strg+O`, bestätige den Dateinamen mit `Enter` und verlasse `nano` mit
`Strg+X`.

Prüfe Original und Backup:

```bash
cat ~/projekte/webshop/config/db.conf
cat ~/projekte/webshop/config/db.conf.backup
```

Das Backup muss `DB_NAME=webshop_dev` enthalten, die Originaldatei
`DB_NAME=webshop_prod`.

<!-- pagebreak -->

## 5. Logs untersuchen und Fehlerbericht schreiben

Lies das Log passend zu seiner Länge:

```bash
head ~/projekte/webshop/logs/access.log
tail ~/projekte/webshop/logs/access.log
less ~/projekte/webshop/logs/access.log
```

Beende `less` mit `q`. Leite die letzten 20 Zeilen in den Fehlerbericht um
und hänge eine Zusammenfassung an:

```bash
tail -n 20 ~/projekte/webshop/logs/access.log > ~/projekte/webshop/docs/fehlerbericht.txt
printf '%s%s\n' \
  'Zusammenfassung: Um 08:13 trat der Fehler ' \
  "'Datenbankverbindung fehlgeschlagen' auf." \
  >> ~/projekte/webshop/docs/fehlerbericht.txt
```

Prüfe den Bericht:

```bash
cat ~/projekte/webshop/docs/fehlerbericht.txt
```

## 6. Ablage aufräumen

Verschaffe dir zuerst einen Überblick:

```bash
ls -la ~/ablage
```

Entferne nur Dateien mit der Endung `.tmp`:

```bash
find ~/ablage -type f -name "*.tmp" -delete
```

Prüfe, dass die temporären Dateien weg sind und die drei anderen Dateien
erhalten blieben:

```bash
find ~/ablage -maxdepth 1 -type f -print
```

<!-- pagebreak -->

## 7. Übergabeprotokoll erstellen

Erstelle ein Protokoll mit der finalen Projektstruktur. Verwende wieder
`tree`, falls vorhanden, und sonst `find`:

```bash
if command -v tree >/dev/null 2>&1; then
    tree -a ~/projekte/webshop > ~/uebergabe-protokoll.txt
else
    find ~/projekte/webshop -print > ~/uebergabe-protokoll.txt
fi
echo "" >> ~/uebergabe-protokoll.txt
echo "Projekt: webshop" >> ~/uebergabe-protokoll.txt
printf '%s%s\n' \
  'Durchgefuehrt: Dateien gefunden und einsortiert, ' \
  'db.conf gesichert und auf Produktion angepasst.' \
  >> ~/uebergabe-protokoll.txt
printf '%s%s\n' \
  'Durchgefuehrt: Fehlerbericht aus dem Log erstellt und ' \
  '.tmp-Dateien aus ~/ablage entfernt.' \
  >> ~/uebergabe-protokoll.txt
```

Prüfe das vollständige Protokoll:

```bash
cat ~/uebergabe-protokoll.txt
```

<!-- pagebreak -->

## Abschlusskontrolle

Dein Capstone ist abgeschlossen, wenn:

- `~/status.txt` Benutzername, Hostname und eine Home-Auflistung enthält,
- unter `~/projekte/webshop/` die fünf Verzeichnisse und `.gitkeep`-Dateien
  vorhanden sind,
- `app.conf`, `db.conf`, `README.md` und `access.log` richtig einsortiert
  wurden,
- `db.conf.backup` den alten Wert `webshop_dev` enthält,
- `db.conf` den neuen Wert `webshop_prod` enthält,
- `fehlerbericht.txt` den Zeitstempel `08:13` und die Fehlermeldung enthält,
- keine `.tmp`-Dateien mehr in `~/ablage` liegen,
- `wichtig.txt`, `kontakte.txt` und `checkliste.md` erhalten blieben,
- `~/uebergabe-protokoll.txt` die Struktur und die ausgeführten Änderungen
  dokumentiert.

## Zusammenfassung

Du hast alle bisherigen Teilmodule in einem realistischen Arbeitsauftrag
verbunden: orientieren, lesen, anlegen, suchen, verschieben, sichern,
bearbeiten, analysieren, aufräumen und dokumentieren. Das Ergebnis ist eine
nachvollziehbare Server-Übergabe statt einer Sammlung unkontrollierter
Einzelbefehle.
