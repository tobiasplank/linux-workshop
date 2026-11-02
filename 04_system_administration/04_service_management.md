# Modul 4.4: Dienste verstehen und steuern

## Anschluss an Modul 4.3

In den letzten Labs hast du Prozesse beobachtet und Software mit `apt`
verwaltet. Jetzt steuerst du Hintergrunddienste mit `systemctl`. Du prüfst
ihren Zustand, stoppst und startest sie, führst einen Neustart durch und
konfigurierst den Autostart.

> [!CAUTION]
> Verändere ausschließlich die vorbereiteten Dienste `cron` und
> `logcollector`. Laufzeitstatus und Autostart sind getrennte Zustände. Entferne
> den Benutzer-Crontab-Eintrag nach der Übung wieder und ändere keine
> Produktivdienste.

<!-- pagebreak -->

## 1. Was sind Dienste?

Ein Dienst (Service oder Daemon) ist ein Prozess, der im Hintergrund läuft
und nicht an ein Terminal gebunden ist. Unter Linux verwaltet `systemd` diese
Dienste. Das Befehlswerkzeug dafür heißt `systemctl`.

Prüfe den vorbereiteten Dienst `cron`:

```bash
systemctl status cron --no-pager
```

Achte auf:

- `Loaded`: Konfiguration geladen und eventuell `enabled`
- `Active`: aktueller Zustand, zum Beispiel `active (running)`
- `Main PID`: Prozess-ID des Dienstes

## 2. Welche Dienste laufen?

Liste aktive Service-Units:

```bash
systemctl list-units --type=service --no-pager
```

Ein Pager kann mit `q` beendet werden. Die Spalten bedeuten:

| Spalte | Bedeutung |
| --- | --- |
| `UNIT` | Name der Service-Unit |
| `LOAD` | Konfiguration geladen |
| `ACTIVE` | übergeordneter Zustand |
| `SUB` | Detailzustand, zum Beispiel `running` oder `exited` |
| `DESCRIPTION` | kurze Beschreibung |

`running` bedeutet, dass der Dienst gerade läuft. `exited` kann bei einem
Dienst normal sein, der eine einmalige Startaufgabe erledigt hat.

## 3. Status eines Dienstes lesen

Lies den vollständigen Status von `cron`:

```bash
systemctl status cron --no-pager
```

Vergleiche `Active: active (running)` mit der `Main PID`. Ein Dienst ist ein
Prozess und besitzt daher ebenfalls eine PID.

## 4. Stoppen und Starten

Stoppe `cron` im Labor:

```bash
sudo systemctl stop cron
systemctl status cron --no-pager
```

Der Zustand sollte `inactive (dead)` sein. Starte ihn wieder:

```bash
sudo systemctl start cron
systemctl status cron --no-pager
```

Der Zustand ist wieder `active (running)`. Die PID kann sich geändert haben.
Auf einem echten Server würden geplante Cronjobs während des Stopps nicht
ausgeführt.

## 5. Restart

Starte `cron` mit einem einzigen Befehl neu:

```bash
sudo systemctl restart cron
systemctl status cron --no-pager
```

`restart` beendet den alten Prozess und startet ihn neu. Das wird oft nach
Konfigurationsänderungen verwendet. Manche Dienste unterstützen zusätzlich
`reload`, um Konfiguration ohne vollständigen Neustart neu einzulesen.

## 6. Autostart mit enable und disable

Prüfe den Autostart:

```bash
systemctl is-enabled cron
```

Deaktiviere ihn für den nächsten Boot:

```bash
sudo systemctl disable cron
systemctl is-enabled cron
```

`disable` stoppt den laufenden Dienst nicht. Es ändert nur den Autostart.
Aktiviere ihn anschließend wieder:

```bash
sudo systemctl enable cron
systemctl is-enabled cron
```

| Befehl | Wirkung |
| --- | --- |
| `start` | jetzt starten |
| `stop` | jetzt stoppen |
| `restart` | stoppen und neu starten |
| `enable` | beim Boot automatisch starten |
| `disable` | Autostart deaktivieren |

## 7. Cron in Aktion

Erstelle einen Cronjob für deinen Benutzer:

```bash
crontab -e
```

Füge diese Zeile ein:

```text
* * * * * date >> /home/student/cron.log
```

Die fünf Sternchen bedeuten: jede Minute, jede Stunde, jeder Tag im Monat,
jeder Monat und jeder Wochentag. Speichere in `nano` mit `Strg+O`, bestätige
mit `Enter` und beende mit `Strg+X`.

Prüfe den Eintrag:

```bash
crontab -l
```

Warte etwa 60 bis 90 Sekunden und prüfe:

```bash
cat ~/cron.log
```

Falls die Datei noch nicht existiert, warte bis zur nächsten vollen Minute.
Entferne den Übungseintrag danach wieder:

```bash
crontab -e
```

Lösche die `date >> ...`-Zeile, speichere und prüfe:

```bash
crontab -l
```

<!-- pagebreak -->

## 8. Einen Dienst retten

Der Dienst `logcollector` sammelt im Labor System-Logmeldungen. Er befindet
sich absichtlich nicht im gewünschten Zustand. Untersuche ihn:

```bash
systemctl status logcollector --no-pager
systemctl is-enabled logcollector
```

Bringe ihn in diesen Zielzustand:

- jetzt aktiv,
- nach dem nächsten Neustart automatisch aktiv.

Verwende selbständig die passenden Befehle aus den vorherigen Aufgaben.
Prüfe danach:

```bash
systemctl status logcollector --no-pager
systemctl is-enabled logcollector
```

## Sicherheitsregeln

- Status und Listen ohne `sudo` lesen; Änderungen an Diensten benötigen
  `sudo`.
- Nur `cron` und den vorbereiteten `logcollector` steuern.
- Keine unbekannten Produktivdienste stoppen oder deaktivieren.
- Cronjobs nach der Übung entfernen.
- Vor `disable` prüfen, welche Auswirkungen ein Neustart hätte.

## Zusammenfassung

Du kannst jetzt:

- Dienststatus mit `systemctl status` lesen,
- aktive Services auflisten,
- Dienste stoppen, starten und neu starten,
- Laufzeitstatus und Autostart mit `is-enabled` unterscheiden,
- einen Cronjob anlegen, prüfen und entfernen,
- einen gestoppten Dienst selbständig in den Zielzustand bringen.

Im nächsten Modul untersuchst du Dienstprotokolle und Fehlerursachen.
