# Modul 4.7: Capstone – Server-Wartung

## Einordnung

Du übernimmst eine Wartungsschicht auf einem Server mit mehreren gleichzeitigen
Problemen. Dieses Capstone fasst die Inhalte aus Modul 4 zusammen: Gesundheit
prüfen, Prozesse steuern, Software installieren, Dienste verwalten,
Fehlerursachen diagnostizieren und Speicher gezielt bereinigen.

Arbeite nach dem Muster:

1. Lagebild aufnehmen
2. Risiken und Reihenfolge beurteilen
3. minimal reparieren
4. jeden Bereich belastbar verifizieren

> [!CAUTION]
> Dieses Capstone verändert Prozesse, Pakete, Dienste und Dateien. Arbeite
> ausschließlich in der vorbereiteten, isolierten und zurücksetzbaren
> Laborumgebung. Beende nur den eindeutig identifizierten Trainingsprozess,
> steuere nur `cron` und lösche nur die vier vorbereiteten Altlasten unter `~`.

<!-- pagebreak -->

## 1. Lagebild

Zu Schichtbeginn liegen zwei Artefakte bereit:

```bash
cat ~/alerts/morning-summary.txt
cat ~/tickets/offene-punkte.txt
```

Bestätige die Hinweise anschließend mit eigenen Diagnosebefehlen. Prüfe alle
vier Bereiche, ohne in diesem Task etwas zu verändern:

- Last und Prozesse
- fehlendes Werkzeug
- Dienstzustand
- Speicherlage

Verwende passende Befehle aus den vorherigen Modulen, zum Beispiel `ps`,
`systemctl status`, `command -v ncdu`, `df -h` und `du`.

## 2. Den CPU-Fresser finden und beenden

Der Server reagiert träge. Identifiziere den vorbereiteten Prozess mit
ungewöhnlich hoher CPU-Auslastung. Sortiere die Prozessliste nach CPU:

```bash
ps aux --sort=-%cpu | head
```

Prüfe den verdächtigen Prozess genau: PID, Benutzer, Befehl und CPU-Wert.
Beende nur den eindeutig vorbereiteten Trainingsprozess:

```bash
kill PID
```

Ersetze `PID` durch die tatsächlich geprüfte Prozess-ID. Prüfe danach:

```bash
ps -p PID
ps aux --sort=-%cpu | head
```

Falls der Prozess nach einer angemessenen Wartezeit nicht beendet ist,
verwende als letzte Eskalation:

```bash
kill -9 PID
```

Keine Sammelsignale verwenden und keine normalen Systemprozesse beenden.

<!-- pagebreak -->

## 3. Das fehlende Tool installieren

Prüfe zuerst, ob `ncdu` wirklich fehlt:

```bash
command -v ncdu
dpkg -l | grep ncdu
```

Installiere es anschließend über die Paketverwaltung:

```bash
sudo apt update
sudo apt install ncdu -y
```

Verifiziere die Installation:

```bash
command -v ncdu
ncdu --version
```

## 4. Den gestoppten Dienst reparieren

Die nächtlichen Backups hängen vom Dienst `cron` ab. Prüfe Zustand und
Autostart:

```bash
systemctl status cron --no-pager
systemctl is-enabled cron
```

Untersuche die letzten Meldungen:

```bash
sudo journalctl -u cron --since "30 min ago" --no-pager
```

Bringe den Dienst in den Zielzustand:

```bash
sudo systemctl start cron
sudo systemctl enable cron
```

Prüfe anschließend beide Eigenschaften:

```bash
systemctl status cron --no-pager
systemctl is-enabled cron
```

Der Dienst muss jetzt `active (running)` und `enabled` sein.

<!-- pagebreak -->

## 5. Speicherplatz freigeben

Verschaffe dir zuerst einen Überblick:

```bash
df -h
```

In dieser Container-Umgebung ist `df -h` nur der grobe Blick auf das vom
Container verwendete Host-Dateisystem. Suche die tatsächlichen Platzfresser
unter deinem Home-Verzeichnis:

```bash
du -sh ~
cd ~
du -sh * | sort -rh
find ~ -type f -size +20M
```

Berücksichtige auch versteckte Inhalte:

```bash
ls -a ~
du -sh ~/.cache/*
```

Prüfe jede Datei, bevor du sie löschst. Entferne nur die
vorbereiteten unnützen großen Dateien unter `~`. Wichtige Dateien und das
Projektverzeichnis bleiben erhalten:

```bash
rm -- /absoluter/pfad/zur/altlast
```

Verwende keine ungeprüfte Wildcard und lösche nichts außerhalb deines
Home-Verzeichnisses.

## 6. Server-Gesundheit verifizieren

Melde die Wartung erst als erfolgreich, wenn du alle vier Bereiche mit
eigenen Prüfkommandos kontrolliert hast:

```bash
ps aux --sort=-%cpu | head
command -v ncdu
ncdu --version
systemctl status cron --no-pager
systemctl is-enabled cron
du -sh ~
find ~ -type f -size +20M
ls -la ~/projekte
```

Der CPU-intensive Trainingsprozess darf nicht mehr laufen. `ncdu` muss
installiert und ausführbar sein. `cron` muss laufen und für den Autostart
aktiviert sein. Die großen Altlasten müssen verschwunden sein, während das
Projektverzeichnis weiterhin vorhanden und vollständig ist.

<!-- pagebreak -->

## Sicherheitsregeln

- Im ersten Task nichts verändern.
- Nur den eindeutig identifizierten CPU-Trainingsprozess beenden.
- Keine Sammelsignale und keine normalen Systemprozesse verwenden.
- Pakete nur aus den freigegebenen Ubuntu-Quellen installieren.
- Nur `cron` als vorbereiteten Dienst verändern.
- Nur die vorbereiteten Altlasten unter `~` löschen.
- Vor jeder Änderung Zustand und Ziel prüfen; danach immer verifizieren.

## Übergabe

Dokumentiere für die Übergabe kurz:

- welche vier Probleme vorlagen,
- welche Diagnosebefehle die Ursachen bestätigt haben,
- welche Änderungen du vorgenommen hast,
- mit welchen Prüfungen du den stabilen Zielzustand belegt hast.

Damit ist die Wartungsschicht nachvollziehbar abgeschlossen.
