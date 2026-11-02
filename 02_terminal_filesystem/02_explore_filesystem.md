# Modul 2.2: Linux-Dateisystem erkunden

## Anschluss an Modul 2.1

In Modul 2.1 hast du `pwd`, `ls`, `cd`, `cat`, `history` und Hilfe-Befehle
kennengelernt. Jetzt setzt du diese Befehle in einem zusammenhängenden
Arbeitsauftrag ein.

Du arbeitest weiterhin auf deinem Ubuntu-Laptop. Da keine Dateien vorbereitet
sind, legst du zuerst einen eigenen Übungsbereich an. Dadurch sind alle
Schritte reproduzierbar und du weißt jederzeit, welche Dateien zur Schulung
gehören.

## Lernziele und Praxisbezug

Nach diesem Modul kannst du:

- eigene Übungsdateien und Verzeichnisse anlegen,
- den Linux-Dateisystembaum einordnen und absolute von relativen Pfaden
  unterscheiden,
- mit `cd`, `~`, `.` und `..` sicher navigieren,
- typische Systembereiche mit `ls` untersuchen,
- kurze und längere Dateien mit passenden Werkzeugen lesen.

Diese Fähigkeiten helfen dabei, vor einer Änderung den aktuellen Standort zu
prüfen, Projektdateien zuverlässig zu finden und Systemkonfigurationen von
Betriebsdaten zu unterscheiden.

<!-- pagebreak -->

## 1. Einen eigenen Übungsbereich anlegen

Wechsle zunächst in dein Home-Verzeichnis und lege dort einen Ordner für die
Schulung an:

```bash
cd ~
mkdir -p ubuntu-schulung/arbeitsbereich
cd ubuntu-schulung
```

`mkdir` erstellt Verzeichnisse. Die Option `-p` legt bei Bedarf auch fehlende
übergeordnete Verzeichnisse an. Prüfe deinen Standort:

```bash
pwd
ls
```

Du solltest jetzt den Ordner `arbeitsbereich` sehen. Er gehört dir und kann
während der Schulung gefahrlos verändert werden.

Erzeuge darin drei kleine Beispieldateien:

```bash
printf 'Projekt: Webshop\nStatus: in Arbeit\n' > arbeitsbereich/projekt.txt
printf '%s\n' \
  'Montag: Anforderungen prüfen' \
  'Dienstag: Testdaten erzeugen' \
  > arbeitsbereich/aufgaben.txt
printf 'Dieser Ordner gehört zur Ubuntu-Schulung.\n' > arbeitsbereich/README.txt
```

`printf` schreibt Text. Das Zeichen `>` leitet die Ausgabe in eine Datei um.
Existiert die Datei noch nicht, wird sie erstellt. So bist du nicht auf
vorbereitete Dateien angewiesen.

Prüfe das Ergebnis:

```bash
ls arbeitsbereich
cat arbeitsbereich/README.txt
```

> [!WARNING]
> `>` überschreibt eine vorhandene Datei. Verwende es nur, wenn du die Datei
> bewusst neu erstellen oder ersetzen möchtest.

<!-- pagebreak -->

## 2. Der Linux-Dateisystembaum

Linux verwendet grundsätzlich einen gemeinsamen Dateisystembaum. Seine Wurzel
ist `/`. Es gibt keine Laufwerksbuchstaben wie `C:\` oder `D:\`.

```text
/
├── home/       Persönliche Verzeichnisse der Benutzer
├── etc/        System- und Programmkonfiguration
├── var/        Veränderliche Daten, zum Beispiel Protokolle
├── tmp/        Temporäre Dateien
├── usr/        Programme und gemeinsam genutzte Dateien
└── dev/        Geräte, die das System bereitstellt
```

Die genaue Ausgabe kann je nach Ubuntu-Version abweichen. Erkunde die oberste
Ebene:

```bash
cd /
pwd
ls
```

Jeder Pfad beschreibt einen Weg durch diesen Baum. `/home/deinname` bedeutet:
von `/` in `home` und dort in dein persönliches Verzeichnis.

## 3. Absolute und relative Pfade

Ein **absoluter Pfad** beginnt mit `/` und funktioniert unabhängig vom aktuellen
Standort:

```bash
cd /var/log
pwd
```

Ein **relativer Pfad** beginnt nicht mit `/`. Er wird vom aktuellen
Arbeitsverzeichnis aus aufgelöst. Wechsle zuerst zurück zu deinem
Übungsbereich:

```bash
cd ~/ubuntu-schulung
cd arbeitsbereich
pwd
```

Du bist jetzt in `~/ubuntu-schulung/arbeitsbereich`. Der Befehl `cd ..` führt
eine Ebene nach oben:

```bash
cd ..
pwd
```

<!-- pagebreak -->

Die Sonderpfade sind:

| Schreibweise | Bedeutung |
| --- | --- |
| `~` | eigenes Home-Verzeichnis |
| `.` | aktuelles Verzeichnis |
| `..` | übergeordnetes Verzeichnis |
| `-` | vorheriges Arbeitsverzeichnis |

Probiere den Wechsel zwischen zwei Orten:

```bash
cd /var/log
cd -
pwd
```

`cd -` bringt dich zu dem Verzeichnis zurück, in dem du unmittelbar vorher
warst. Mit `cd ~` gelangst du jederzeit nach Hause.

## 4. Wichtige Systembereiche lesen

### Konfiguration

In `/etc` liegen viele Konfigurationsdateien. Du kannst einige davon lesen:

```bash
ls /etc
cat /etc/hostname
cat /etc/hosts
```

Die Datei `/etc/hostname` enthält den Hostnamen. `/etc/hosts` enthält lokale
Zuordnungen von Namen und IP-Adressen. Welche zusätzlichen Dateien vorhanden
sind, hängt von der Ubuntu-Installation ab.

### Veränderliche Daten und Logs

Unter `/var` liegen Daten, die sich während des Betriebs ändern. Die
Protokolldateien liegen häufig unter `/var/log`:

```bash
ls /var
ls /var/log
```

<!-- pagebreak -->

Die Namen und Zugriffsrechte der Logdateien unterscheiden sich je nach System.
Zeige deshalb zunächst nur die Liste an. Öffne anschließend eine Datei, die
lesbar ist, nur mit den ersten Zeilen:

```bash
head /var/log/dpkg.log
```

Falls die Datei nicht existiert oder nicht gelesen werden darf, ist das auf
einem anderen Ubuntu-System normal. Nutze dann:

```bash
ls -l /var/log
```

Wichtig ist das Prinzip: `/etc` beschreibt, wie das System eingerichtet ist;
`/var` enthält Daten, die sich im Betrieb verändern.

## 5. Dateien genauer untersuchen

Gehe zurück in deinen eigenen Arbeitsbereich:

```bash
cd ~/ubuntu-schulung/arbeitsbereich
```

Die einfache Ansicht zeigt nur Namen:

```bash
ls
```

Mit `-l` siehst du eine Detailansicht:

```bash
ls -l
```

Die Spalten enthalten unter anderem:

- **Typ und Rechte:** Ein `d` am Anfang steht für ein Verzeichnis.
- **Besitzer und Gruppe:** Sie zeigen, wem der Eintrag zugeordnet ist.
- **Größe:** Die Dateigröße wird standardmäßig in Bytes angezeigt.
- **Zeitpunkt:** Zeitpunkt der letzten Änderung.
- **Name:** Name der Datei oder des Verzeichnisses.

Mit `-a` werden auch versteckte Dateien angezeigt:

```bash
ls -la
```

Dateien, deren Name mit `.` beginnt, sind versteckt. Linux verwendet solche
Dotfiles häufig für persönliche Programmeinstellungen. Die Einträge `.` und
`..` gehören zu jedem Verzeichnis.

<!-- pagebreak -->

## 6. Inhalte lesen, ohne das Terminal zu überfluten

Lies die selbst angelegten Dateien:

```bash
cat projekt.txt
cat aufgaben.txt
```

`cat` ist für kurze Dateien praktisch. Für längere Dateien sind diese Befehle
übersichtlicher:

```bash
head -n 1 aufgaben.txt
tail -n 1 aufgaben.txt
```

`head` zeigt den Anfang, `tail` das Ende einer Datei. Mit `-n 1` begrenzt du
die Ausgabe auf eine Zeile. `less` öffnet eine Datei in einem Lesemodus:

```bash
less aufgaben.txt
```

Mit den Pfeiltasten scrollst du, mit `q` verlässt du `less`.

## 7. Abschlussaufgabe

Bearbeite diese Aufgabe ohne eine vorgegebene Befehlsfolge. Nutze die bisher
gelernten Befehle und lies bei Bedarf `man` oder `--help`.

1. Wechsle direkt mit einem absoluten oder mit `~` beginnenden Pfad in
   `~/ubuntu-schulung/arbeitsbereich`.
2. Zeige alle Dateien in der Detailansicht an.
3. Lies nur die erste Zeile von `projekt.txt`.
4. Wechsle nach `/etc` und finde mit `ls` die Datei `hostname`.
5. Lies den Inhalt von `/etc/hostname`.
6. Kehre mit `cd -` oder `cd ~` zu deinem eigenen Arbeitsbereich zurück.
7. Zeige mit `pwd` und `ls` nach, dass du am richtigen Ort bist.

<!-- pagebreak -->

## Transfer in den Arbeitsalltag

Übertrage das Vorgehen auf deine Fachrichtung:

- **Systemintegration:** Prüfe vor Änderungen immer Hostname, Benutzer und
  Arbeitsverzeichnis. Suche Konfigurationen zunächst in `/etc` und Logs in
  `/var/log`.
- **Anwendungsentwicklung:** Lege pro Projekt einen klaren Arbeitsbereich an
  und verwende relative Pfade nur dann, wenn dein aktueller Standort eindeutig
  ist.
- **Daten- und Prozessanalyse:** Nutze `ls -l`, `head`, `tail` und `less`, um
  Dateien zuerst zu prüfen, bevor du sie weiterverarbeitest.

## Zusammenfassung

Du kannst jetzt:

- eigene Übungsdateien und Verzeichnisse anlegen,
- den Linux-Dateisystembaum von `/` aus einordnen,
- absolute und relative Pfade unterscheiden,
- mit `cd`, `~`, `.` und `..` sicher navigieren,
- Konfigurationen und Logs an typischen Orten finden,
- Dateityp, Besitzer, Größe und versteckte Dateien prüfen,
- kurze und längere Dateien passend lesen.

Damit bilden die beiden Module einen durchgängigen Einstieg: Erst orientierst du
dich im Terminal, danach nutzt du diese Orientierung, um einen eigenen
Arbeitsbereich und das Linux-Dateisystem systematisch zu erkunden.
