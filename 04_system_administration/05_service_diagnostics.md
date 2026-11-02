# Modul 4.5: Dienste diagnostizieren

## Anschluss an Modul 4.4

Im letzten Modul hast du Dienste mit `systemctl` gesteuert. Jetzt
diagnostizierst du Fehler, statt einen Dienst einfach nur neu zu starten.
Du kombinierst Status, Logs und Service-Dateien und behebst anschließend die
eigentliche Ursache.

> [!CAUTION]
> Dieses Lab ändert systemweite Service-Dateien und Rechte. Arbeite nur in der
> vorbereiteten, isolierten und zurücksetzbaren Laborumgebung und verändere
> ausschließlich die beschriebenen Fixtures. Repariere minimal und stelle
> anschließend den dokumentierten Ausgangszustand wieder her.

<!-- pagebreak -->

## 1. Ein Dienst funktioniert nicht

Der Dienst `cron` steuert auf diesem Server nächtliche Aufgaben. Prüfe zuerst
seinen Zustand:

```bash
systemctl status cron --no-pager
```

Der Dienst ist im Labor zunächst `inactive (dead)`. Starte ihn nicht sofort.
Bei einer Diagnose fragst du zuerst: Ist er absichtlich gestoppt, abgestürzt
oder falsch konfiguriert? Die Antwort steht oft in den Logs.

## 2. Das Systemlog lesen

`systemd` sammelt Meldungen vieler Dienste in einem Journal. Das Werkzeug zum
Lesen heißt `journalctl`.

```bash
sudo journalctl
```

Die Ausgabe läuft in einem Pager. Mit den Pfeiltasten navigierst du, mit `q`
beendest du ihn. Eine Journalzeile enthält typischerweise:

| Teil | Bedeutung |
| --- | --- |
| Zeitstempel | Zeitpunkt der Meldung |
| Quelle | Dienst oder Prozess, zum Beispiel `cron[1234]` |
| Nachricht | Ereignis, Hinweis oder Fehler |

Für vollständige Systemlogs brauchst du häufig `sudo`. Ohne diese Rechte
kannst du nur eingeschränkte Benutzerlogs sehen.

## 3. Logs eines Dienstes filtern

Filtere das Journal auf `cron`:

```bash
sudo journalctl -u cron --no-pager
```

`-u` steht für Unit, also die systemd-Bezeichnung eines Dienstes. Begrenze
die Ausgabe zusätzlich auf die letzten zehn Minuten:

```bash
sudo journalctl -u cron --since "10 min ago" --no-pager
```

<!-- pagebreak -->

Starte `cron` danach bewusst:

```bash
sudo systemctl start cron
sudo journalctl -u cron --since "2 min ago" --no-pager
```

Prüfe, ob die neuen Zeilen aktuell sind und keine Meldungen wie `failed`,
`error` oder `no such file` enthalten. Die genaue Meldung ist abhängig von
Distribution und Version; entscheidend sind Zeitstempel, Quelle und Inhalt.

## 4. Logs in Echtzeit verfolgen

Mit `-f` (follow) bleibt `journalctl` geöffnet und zeigt neue Einträge sofort:

```bash
sudo journalctl -f -t lab-heartbeat
```

Der vorbereitete Prozess schreibt regelmäßig Meldungen mit dem Tag
`lab-heartbeat`. Warte einige Sekunden und beobachte, wie neue Zeilen
erscheinen. Beende die Live-Ansicht mit `Strg+C`.

## 5. Service-Dateien verstehen

Service-Dateien beschreiben, was `systemd` starten soll. Liste die lokalen
Dateien:

```bash
ls /etc/systemd/system/
```

Lies die beiden Dateien dieses Labs:

```bash
cat /etc/systemd/system/webmonitor.service
cat /etc/systemd/system/logrotate-custom.service
```

| Abschnitt | Zweck |
| --- | --- |
| `[Unit]` | Beschreibung und Abhängigkeiten |
| `[Service]` | Startbefehl und Laufzeitumgebung |
| `[Install]` | Einbindung beim Aktivieren des Autostarts |

Achte besonders auf:

- `ExecStart=`: Programm oder Script, das gestartet wird
- `User=`: Benutzer, unter dem es läuft
- `WorkingDirectory=`: Startverzeichnis des Prozesses

<!-- pagebreak -->

Ein falscher Pfad, fehlendes Ausführungsrecht oder ein nicht vorhandenes
Arbeitsverzeichnis kann den gesamten Start verhindern.

Nach einer Änderung an einer Service-Datei muss `systemd` die Konfiguration
neu einlesen:

```bash
sudo systemctl daemon-reload
```

## 6. webmonitor diagnostizieren und reparieren

Arbeite nach diesem Ablauf:

1. Status prüfen:

   ```bash
   systemctl status webmonitor --no-pager
   ```

2. Logs eingrenzen:

   ```bash
   sudo journalctl -u webmonitor --since "10 min ago" --no-pager
   ```

3. Service-Datei und Startprogramm prüfen:

   ```bash
   cat /etc/systemd/system/webmonitor.service
   ls -l /opt/scripts/
   ```

   Vergleiche den `ExecStart`-Pfad mit der tatsächlich vorhandenen Datei.
   Achte auch auf das Ausführungsrecht.

4. Behebe die Ursache mit der kleinsten passenden Änderung. In diesem Lab
   ist das Programm vorhanden, aber nicht ausführbar:

   ```bash
   sudo chmod +x /opt/scripts/webmonitor.sh
   sudo systemctl restart webmonitor
   ```

5. Prüfe Ergebnis und frische Logs:

   ```bash
   systemctl status webmonitor --no-pager
   sudo journalctl -u webmonitor --since "2 min ago" --no-pager
   ```

`status=203/EXEC` ist ein typischer Hinweis darauf, dass `systemd` das
Programm aus `ExecStart` nicht ausführen konnte.

<!-- pagebreak -->

## 7. logrotate-custom selbstständig reparieren

Diagnostiziere den zweiten defekten Dienst:

```bash
systemctl status logrotate-custom --no-pager
sudo journalctl -u logrotate-custom --since "10 min ago" --no-pager
cat /etc/systemd/system/logrotate-custom.service
ls -l /opt/scripts/
```

Vergleiche den in `ExecStart=` genannten Pfad mit den vorhandenen Dateien.
Korrigiere nur den einzelnen falschen Pfad in der Service-Datei. Verwende
dazu einen Editor und ändere keine anderen Einstellungen.

Lies die Datei danach erneut und lade die Konfiguration neu:

```bash
cat /etc/systemd/system/logrotate-custom.service
sudo systemctl daemon-reload
sudo systemctl restart logrotate-custom
```

Prüfe anschließend Status und Logs:

```bash
systemctl status logrotate-custom --no-pager
sudo journalctl -u logrotate-custom --since "2 min ago" --no-pager
```

Ein `oneshot`-Dienst muss nach erfolgreicher Ausführung nicht dauerhaft
`active (running)` anzeigen. Entscheidend ist, dass der Start erfolgreich war
und der aktuelle Fehler nicht mehr auftritt.

<!-- pagebreak -->

## Sicherheitsregeln

- Verändere nur die vier für dieses Lab genannten Fixtures.
- Starte oder repariere keine unbekannten Produktivdienste.
- Ändere nur das notwendige Ausführungsrecht und den fehlerhaften Pfad.
- Service-Dateien unter `/etc/systemd/system/` sind systemweite Konfiguration.
  Dieses Lab gehört deshalb ausschließlich auf eine dedizierte,
  zurücksetzbare Laborumgebung.

## Zusammenfassung

Du kannst jetzt:

- den aktuellen Zustand und die Historie eines Dienstes unterscheiden,
- Logs mit `journalctl` nach Unit, Zeit und Tag filtern,
- Live-Logs mit `journalctl -f` verfolgen,
- Service-Dateien und `ExecStart` prüfen,
- Rechte- und Pfadfehler gezielt beheben,
- nach Änderungen `daemon-reload` ausführen und das Ergebnis verifizieren.
