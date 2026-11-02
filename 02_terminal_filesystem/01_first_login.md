# Modul 2.1: Sicher im Ubuntu-Terminal starten

## Einordnung

In diesem Modul lernst du die wichtigsten Grundlagen für die Arbeit im
Ubuntu-Terminal. Du arbeitest direkt auf deinem Ubuntu-Laptop.

## Lernziele und Praxisbezug

Die Inhalte sind für alle Fachrichtungen relevant:

- **Systemintegration:** Server, Benutzer und Fehlersuche beginnen oft im Terminal.
- **Anwendungsentwicklung:** Programme werden ausgeführt und getestet.
- **Daten- und Prozessanalyse:** Dateien und Verzeichnisse werden untersucht.

Am Ende kannst du dich orientieren, einfache Informationen über dein System
abrufen, Hilfe finden und einen laufenden Befehl sicher abbrechen.

## Terminal und Shell

Öffne auf deinem Ubuntu-Laptop ein Terminal. Das geht zum Beispiel mit
`Strg`+`Alt`+`T`.

Terminal und Shell gehören zusammen, sind aber nicht dasselbe:

- Das **Terminal** ist das Fenster beziehungsweise die grafische Anwendung,
  die Tastatureingaben und Ausgaben anzeigt. Es ist die Oberfläche, in der du
  arbeitest.
- Die **Shell** ist das Kommandozeilenprogramm, das im Terminal läuft. Sie
  liest deine Eingaben, interpretiert Befehle und startet die passenden
  Programme. Auf Ubuntu ist häufig **Bash** die verwendete Shell.

> [!TIP]
> Das Terminal ist wie ein Telefon, über das du sprichst. Die Shell ist die
> Gesprächspartnerin, die deine Anweisung versteht und ausführt. Für die
> folgenden Übungen genügt die Shell, die beim Öffnen bereits gestartet wurde.

Nach jedem Befehl drückst du `Enter`. Die Shell verarbeitet die Eingabe und
zeigt das Ergebnis wieder im Terminal an.

### Erste Funktionsprüfung

Gib Folgendes ein:

```bash
echo "Terminal funktioniert"
```

`echo` gibt den übergebenen Text aus. Wenn der Text erscheint, kannst du
starten.

## 1. Wer bin ich und auf welchem Rechner arbeite ich?

Linux ist ein Mehrbenutzersystem. Dein Benutzerkonto bestimmt, welche Dateien
du lesen, ändern oder löschen darfst. Der Benutzer `root` hat weitreichende
Rechte. In dieser Schulung arbeitest du mit deinem normalen Benutzerkonto und
verwendest keine Befehle mit `sudo`, sofern eine Aufgabe das nicht ausdrücklich
verlangt.

> **Arbeitsweise:** Lies den Befehl zuerst, tippe ihn dann selbst ein und
> vergleiche anschließend deine Ausgabe mit der Beschreibung.

Finde deinen Benutzernamen heraus:

```bash
whoami
```

Finde den Namen deines Laptops heraus:

```bash
hostname
```

Der Hostname kann auf einem Schulungsgerät weniger aussagekräftig sein als ein
Name wie `web-prod-01`. Trotzdem ist die Abfrage wichtig: Auf einem echten
Server solltest du immer prüfen, auf welchem System du gerade arbeitest.

## 2. Wo arbeite ich gerade?

Jeder Terminal-Befehl wird in einem aktuellen **Arbeitsverzeichnis** ausgeführt.
Wenn du eine Datei ohne vollständigen Pfad anlegst, entsteht sie dort.

```bash
pwd
```

`pwd` steht für `print working directory`. Nach dem Öffnen des Terminals
befindest du dich normalerweise in deinem persönlichen Home-Verzeichnis, zum
Beispiel:

```text
/home/deinname
```

Das Home-Verzeichnis ist dein persönlicher Arbeitsbereich. Das Kürzel `~`
steht für dein Home-Verzeichnis:

```bash
pwd
cd ~
pwd
```

Die letzte Ausgabe sollte wieder dein Home-Verzeichnis zeigen. `cd` bedeutet
`change directory` und wird im nächsten Modul ausführlicher behandelt.

## 3. Was liegt hier?

Mit `ls` listest du den Inhalt eines Verzeichnisses auf:

```bash
ls
```

Du kannst auch ausdrücklich dein Home-Verzeichnis angeben:

```bash
ls ~
```

Ohne Argument verwendet `ls` das aktuelle Arbeitsverzeichnis. Mit einem
Argument, hier `~`, listet `ls` genau diesen Ort auf.

Zum Vergleich kannst du ein Systemverzeichnis anzeigen:

```bash
ls /etc
```

`/etc` enthält Konfigurationsdateien des Systems. Du darfst den Inhalt in der
Regel lesen, solltest dort aber nicht ohne konkreten Auftrag Dateien ändern.

## 4. Lange Befehle sicher eingeben: Tab-Vervollständigung

Die Tab-Taste ergänzt Befehle und Pfade. Beginne mit:

```bash
cat /etc/hostn
```

Drücke jetzt `Tab`. Die Shell ergänzt den Pfad normalerweise zu
`/etc/hostname`. Drücke anschließend `Enter`.

`cat` gibt den Inhalt einer Datei aus. In diesem Beispiel liest du den
Hostnamen aus einer Datei:

```bash
cat /etc/hostname
```

Wenn `Tab` nicht ergänzt, drücke die Taste ein zweites Mal. Dann zeigt die
Shell passende Möglichkeiten an. Tab-Vervollständigung verhindert Tippfehler in langen Pfaden.

<!-- pagebreak -->

## 5. Wenn ein Befehl scheinbar hängt

Einige Befehle geben während ihrer Laufzeit nichts aus. Das ist nicht
automatisch ein Fehler. Teste das kontrolliert:

```bash
sleep 300
```

Der Befehl wartet fünf Minuten. Warte nicht, sondern brich ihn mit
`Strg`+`C` ab. Du erhältst deine Eingabezeile zurück.

`Strg`+`C` beendet den aktuell laufenden Vordergrundprozess. Das Terminal selbst
bleibt geöffnet. Verwende diese Tastenkombination, wenn ein Befehl unerwartet
lange läuft oder du eine Eingabe korrigieren möchtest.

## 6. Hilfe selbst finden

Niemand muss alle Linux-Befehle und Optionen auswendig kennen. Entscheidend ist,
die passende Dokumentation zu finden.

Für eine kurze Übersicht:

```bash
ls --help
```

Für die ausführliche Dokumentation:

```bash
man ls
```

In der Manpage navigierst du mit den Pfeiltasten oder der Leertaste. Mit `q`
verlässt du sie. Falls `man` auf deinem Laptop nicht verfügbar ist, verwende
zunächst `ls --help`; die Schulung kann damit fortgesetzt werden.

Begriffe wie `--help` und `-l` heißen **Optionen** oder **Flags**. Sie verändern
das Verhalten eines Befehls, ohne einen neuen Befehl zu bilden.

## 7. Befehle wiederverwenden

Die Shell merkt sich deine Eingaben:

```bash
history
```

Mit der Pfeil-hoch-Taste holst du den letzten Befehl zurück. Drücke sie
mehrfach, um ältere Befehle zu erreichen. Das ist schneller und zuverlässiger,
als lange Befehle erneut zu tippen.

<!-- pagebreak -->

## 8. Abschlussaufgabe

Führe die folgenden Schritte selbstständig aus:

1. Zeige deinen Benutzernamen an.
2. Zeige den Hostnamen an.
3. Prüfe dein aktuelles Arbeitsverzeichnis.
4. Liste dein Home-Verzeichnis auf.
5. Zeige die Hilfe für `pwd` an.
6. Rufe die letzten Befehle aus deiner History auf.

Verwende nur Befehle, die in diesem Modul vorkamen. Wenn du nicht weiterkommst,
nutze `--help`, `man` oder die Pfeil-hoch-Taste.

## Zusammenfassung

Du kannst jetzt:

- Benutzer, Rechner und Arbeitsverzeichnis unterscheiden,
- Verzeichnisinhalte mit `ls` anzeigen,
- Pfade mit Tab vervollständigen,
- einen laufenden Befehl mit `Strg`+`C` abbrechen,
- Hilfe und frühere Befehle selbst finden.

Im nächsten Modul nutzt du diese Grundlagen, um einen eigenen Übungsbereich
anzulegen und den Linux-Dateisystembaum zu erkunden.
