# Modul 2.5: Finden und Zusammenführen

## Anschluss an Modul 2.4

In Modul 2.4 hast du Dateien gelesen und eine Konfiguration mit `nano`
geändert. Jetzt suchst du Dateien in einer verschachtelten Projektstruktur und
führst Ausgaben zu einem schriftlichen Statusbericht zusammen.

## Lernziele und Praxisbezug

Nach diesem Modul kannst du:

- Text mit `echo` ausgeben,
- Ausgaben mit `>` in eine Datei schreiben,
- mit `>>` weitere Ausgaben anhängen,
- mit `find` Dateien und Verzeichnisse rekursiv suchen,
- mit `-name`, `-type` und `-mtime` gezielt filtern,
- einen Bericht über gefundene Konfigurationsdateien erstellen.

In einem übernommenen Projekt liegen wichtige Dateien oft in verschiedenen
Unterverzeichnissen. Ein Bericht macht sichtbar, welche Konfigurationen
existieren und welche möglicherweise veraltet sind.

> [!WARNING]
> Alle schreibenden Befehle bleiben auf `~/ubuntu-schulung/find-labor` und
> `~/berichte` beschränkt. `>` überschreibt eine vorhandene Datei. Prüfe den
> Zielpfad vor dem Schreiben und verwende keine echten Projekt- oder
> Zugangsdaten.

<!-- pagebreak -->

## 1. Ein undokumentiertes Projekt anlegen

Erzeuge einen eigenen Übungsbereich mit einer typischen Projektstruktur:

```bash
mkdir -p ~/ubuntu-schulung/find-labor/projekt/{config,src,services,docs,logs,tmp,backup}
cd ~/ubuntu-schulung/find-labor
```

Lege Konfigurationsdateien an unterschiedlichen Stellen an:

```bash
printf 'port=8080\nlog_level=info\n' > projekt/config/app.conf
printf 'worker_count=2\n' > projekt/services/web.conf
printf 'backup_enabled=true\n' > projekt/backup/backup.conf
printf 'Projekt ohne zentrale Dokumentation.\n' > projekt/docs/README.txt
```

Erzeuge zusätzlich eine ältere Beispielkonfiguration. `touch -d` setzt dabei
nur für die Übung einen vergangenen Änderungszeitpunkt:

```bash
printf 'port=8000\n' > projekt/tmp/old.conf
touch -d '40 days ago' projekt/tmp/old.conf
```

Verschaffe dir zunächst einen Überblick:

```bash
ls projekt
```

`ls` zeigt hier nur die oberste Ebene. Die Dateien in den Unterverzeichnissen
findest du im nächsten Schritt.

## 2. Text mit echo ausgeben

`echo` gibt Text im Terminal aus:

```bash
echo Hallo Terminal
echo "Projekt: Webprojekt (Status: aktiv)"
```

Anführungszeichen schützen zusammengehörigen Text. Sie sind besonders
wichtig, wenn Leerzeichen oder Sonderzeichen vorkommen. `echo` eignet sich
auch für kurze Statusmeldungen und Überschriften in Berichten.

<!-- pagebreak -->

## 3. Ausgaben in Dateien schreiben

Mit `>` wird die Ausgabe in eine Datei umgeleitet. Existiert die Datei nicht,
wird sie erstellt:

```bash
mkdir -p ~/ubuntu-schulung/find-labor/aufgaben
echo "Projektuebernahme gestartet" > ~/ubuntu-schulung/find-labor/aufgaben/notiz.txt
cat ~/ubuntu-schulung/find-labor/aufgaben/notiz.txt
```

Das Terminal zeigt bei erfolgreicher Umleitung normalerweise nichts an. Die
Ausgabe steht in der Datei.

### Überschreiben und Anhängen unterscheiden

Dieser Befehl ersetzt den bisherigen Inhalt:

```bash
echo "Zweite Zeile" > ~/ubuntu-schulung/find-labor/aufgaben/notiz.txt
cat ~/ubuntu-schulung/find-labor/aufgaben/notiz.txt
```

Mit `>>` bleibt der alte Inhalt erhalten und die neue Ausgabe wird angehängt:

```bash
echo "Dritte Zeile" >> ~/ubuntu-schulung/find-labor/aufgaben/notiz.txt
cat ~/ubuntu-schulung/find-labor/aufgaben/notiz.txt
```

| Schreibweise | Wirkung |
| --- | --- |
| `>` | Datei erstellen oder vollständig überschreiben |
| `>>` | Ausgabe am Ende der Datei anhängen |

<!-- pagebreak -->

## 4. Dateien mit find suchen

`find` durchsucht einen Startpfad und alle darunterliegenden Verzeichnisse.
Suche alle Konfigurationsdateien im Projekt:

```bash
find ~/ubuntu-schulung/find-labor/projekt -name "*.conf"
```

Die Anführungszeichen sind wichtig: So erhält `find` das Muster `*.conf` und
die Shell versucht nicht, es schon im aktuellen Verzeichnis aufzulösen.

Beschränke die Suche auf reguläre Dateien:

```bash
find ~/ubuntu-schulung/find-labor/projekt -type f -name "*.conf"
```

`-type f` bedeutet **file**, also normale Datei. Mit `-type d` würdest du nur
Verzeichnisse finden:

```bash
find ~/ubuntu-schulung/find-labor/projekt -type d
```

## 5. Nach dem Änderungsalter filtern

Mit `-mtime` prüfst du, wie viele Tage seit der letzten Änderung vergangen
sind:

| Ausdruck | Bedeutung |
| --- | --- |
| `-mtime +30` | vor mehr als 30 Tagen geändert |
| `-mtime -7` | innerhalb der letzten sieben Tage geändert |
| `-mtime 0` | heute geändert |

Finde die alten Konfigurationsdateien:

```bash
find ~/ubuntu-schulung/find-labor/projekt -type f -name "*.conf" -mtime +30
```

Die Datei `projekt/tmp/old.conf` sollte erscheinen. Alter allein beweist
nicht, dass eine Datei gelöscht werden darf. Markiere sie nur als
prüfenswert und ändere oder lösche sie nicht.

<!-- pagebreak -->

## 6. Selbstständige Aufgabe: Statusbericht erstellen

Erstelle den Bericht für die Teamleitung:

```bash
mkdir -p ~/berichte
```

Die Datei soll `~/berichte/config-report.txt` heißen und enthalten:

- eine Überschrift,
- das aktuelle Datum,
- alle `.conf`-Dateien im Projekt,
- die Anzahl der gefundenen Konfigurationsdateien,
- einen Hinweis auf ältere Dateien.

Verwende `>` nur für die erste Zeile und `>>` für alle weiteren Ausgaben.
Die folgenden Befehle zeigen das Vorgehen; passe die Texte und die gezählte
Anzahl selbst an:

```bash
echo "=== Konfigurationsbericht ===" > ~/berichte/config-report.txt
date >> ~/berichte/config-report.txt
echo "Gefundene Konfigurationsdateien:" >> ~/berichte/config-report.txt
find ~/ubuntu-schulung/find-labor/projekt -type f -name "*.conf" >> ~/berichte/config-report.txt
echo "Anzahl: 4" >> ~/berichte/config-report.txt
echo "Aelter als 30 Tage:" >> ~/berichte/config-report.txt
find ~/ubuntu-schulung/find-labor/projekt -type f -name "*.conf" -mtime +30 >> ~/berichte/config-report.txt
```

Prüfe deinen Bericht:

```bash
cat ~/berichte/config-report.txt
```

Akzeptanzkriterien:

- Der Bericht liegt unter `~/berichte/config-report.txt`.
- Das Datum wurde mit `date` erzeugt.
- Alle vier `.conf`-Dateien werden aufgelistet.
- Die Anzahl ist korrekt.
- `projekt/tmp/old.conf` erscheint im Abschnitt für alte Dateien.
- Der Bericht wurde schrittweise mit `>` und `>>` aufgebaut.

<!-- pagebreak -->

## Transfer in den Arbeitsalltag

- **Systemintegration:** Finde Konfigurationen und alte Dateien, bevor du
  einen übernommenen Dienst dokumentierst oder bereinigst.
- **Anwendungsentwicklung:** Erstelle reproduzierbare Statusberichte aus
  Projektstruktur und Konfigurationsdateien.
- **Daten- und Prozessanalyse:** Trenne die Datensuche von der Bewertung und
  dokumentiere Kriterien wie Dateiname, Typ und Änderungsalter.

## Zusammenfassung

Du kannst jetzt Text ausgeben, Ausgaben überschreiben oder anhängen und mit
`find` gezielt Dateien aufspüren. Aus diesen Bausteinen hast du einen
weiterleitbaren Statusbericht über ein unbekanntes Projekt erstellt.
