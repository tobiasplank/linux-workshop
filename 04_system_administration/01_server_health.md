# Modul 4.1: Server-Gesundheit prüfen

## Einordnung

Der Berechtigungsblock ist abgeschlossen. Jetzt übernimmst du einen Server,
den du noch nicht kennst. Bevor du etwas änderst, prüfst du Prozesse,
Auslastung, Arbeitsspeicher und Laufzeit.

> [!NOTE]
> Dieses Lab ist ausschließlich lesend: Beende keine Prozesse, ändere keine
> Dienste und installiere keine Pakete. PIDs, Speicherwerte, Laufzeiten und
> Load-Average-Werte sind Momentaufnahmen und können auf deinem System anders
> aussehen.

<!-- pagebreak -->

## 1. Übergabe: Dein Server, deine Verantwortung

Lies Kais Übergabe-Notiz:

```bash
cat ~/notizen/uebergabe.txt
```

Merke dir die genannten Dienste. Du wirst sie gleich in der Prozessliste
wiederfinden.

Dein Diagnoseablauf lautet:

1. Was läuft auf dem Server?
2. Wie stark ist der Server ausgelastet?
3. Ist der Zustand auffällig oder plausibel?

## 2. Der erste Blick mit ps

Ein Prozess ist ein laufendes Programm. Jeder Prozess hat eine PID
(Process ID), einen Besitzer und einen gestarteten Befehl.

Zeige die Prozesse deiner aktuellen Terminal-Sitzung:

```bash
ps
```

Die Spalten bedeuten:

- `PID`: eindeutige Prozessnummer
- `TTY`: zugehöriges Terminal
- `TIME`: verbrauchte CPU-Zeit
- `CMD`: gestarteter Befehl

`ps` ohne Optionen zeigt nur einen kleinen Ausschnitt. Hintergrunddienste
fehlen meistens.

## 3. Das große Bild mit ps aux

Zeige alle Prozesse:

```bash
ps aux
```

Die Optionen bedeuten:

| Option | Bedeutung |
| --- | --- |
| `a` | Prozesse aller Benutzer mit Terminal |
| `u` | ausführliches, benutzerfreundliches Format |
| `x` | auch Prozesse ohne Terminal |

<!-- pagebreak -->

Achte besonders auf `USER`, `PID`, `%CPU`, `%MEM` und `COMMAND`. `ps aux` ist
eine Momentaufnahme. Ein Wert kann sich direkt danach schon verändern.

Suche bei Bedarf gezielt nach einem Dienst aus der Übergabe-Notiz:

```bash
ps aux | grep '[s]uchbegriff'
```

Ersetze `suchbegriff` durch einen tatsächlichen Namen aus der Notiz.

## 4. Live beobachten mit top

Starte die Live-Ansicht:

```bash
top
```

Die Kopfzeile zeigt unter anderem Laufzeit, Prozessanzahl, CPU- und
Speichernutzung. Darunter stehen die Prozesse.

Während `top` läuft:

- `M` sortiert nach Speicherverbrauch,
- `P` sortiert nach CPU-Verbrauch,
- `q` beendet `top`.

Beobachte kurz, ob ein Prozess dauerhaft auffällig viel CPU oder Speicher
verbraucht. Ein einzelner kurzer Ausschlag ist noch kein Beweis für ein
Problem.

## 5. Alternative: htop

Prüfe zunächst, ob `htop` im Labor vorhanden ist:

```bash
command -v htop
```

Falls ein Pfad ausgegeben wird, starte es:

```bash
htop
```

`htop` stellt CPU, Speicher und Swap farbig dar. Mit `t` oder `F5` kannst du
die Prozessbaumansicht ein- und ausschalten. Mit `q` beendest du das Programm.

Falls `htop` nicht installiert ist, ist das kein Fehler. Verwende `top`
weiter und installiere in diesem Lab nichts nach.

<!-- pagebreak -->

## 6. Arbeitsspeicher mit free

Prüfe den Speicher in lesbaren Einheiten:

```bash
free -h
```

Wichtige Spalten:

| Spalte | Bedeutung |
| --- | --- |
| `total` | gesamter Arbeitsspeicher |
| `used` | aktuell verwendeter Speicher |
| `free` | unmittelbar ungenutzter Speicher |
| `buff/cache` | für Beschleunigung verwendeter Cache |
| `available` | Speicher, der neuen Programmen voraussichtlich zur Verfügung steht |

Bewerte vor allem `available`, nicht nur `free`. Linux verwendet freien
Speicher gern als Cache und gibt ihn bei Bedarf wieder frei. Ein niedriger
`free`-Wert bedeutet deshalb nicht automatisch ein Problem.

## 7. Laufzeit und Load Average mit uptime

Lies Laufzeit und Last:

```bash
uptime
```

Eine Beispielausgabe:

```text
14:23:07 up 2 min, 1 user, load average: 0.52, 0.25, 0.10
```

Die drei Load-Average-Werte gelten für 1, 5 und 15 Minuten. Bei einem
einzelnen CPU-Kern bedeutet ein Wert unter 1.0 normalerweise, dass kein
anhaltender CPU-Wartestau besteht. Bei mehreren Kernen musst du den Wert im
Verhältnis zur Kernanzahl betrachten.

Vergleiche die drei Werte:

- erste Zahl kleiner als dritte: die Last nimmt gerade ab,
- erste Zahl größer als dritte: die Last steigt gerade,
- dauerhaft hohe Werte: genauer mit `top` und den Prozessen untersuchen.

<!-- pagebreak -->

## Abschlussaufgabe: Gesundheitsbewertung

Erstelle eine kurze Notiz für die nächste Schicht:

```bash
printf '=== Servercheck ===\n' > ~/servercheck.txt
printf 'Prozesse:\n' >> ~/servercheck.txt
ps aux | head -n 8 >> ~/servercheck.txt
printf 'Speicher:\n' >> ~/servercheck.txt
free -h >> ~/servercheck.txt
printf 'Laufzeit und Load:\n' >> ~/servercheck.txt
uptime >> ~/servercheck.txt
```

Ergänze anschließend eine eigene Bewertung mit `nano`. Beziehe dich auf:

- mindestens einen beobachteten Dienst oder Prozess,
- `available` aus `free -h`,
- die drei Load-Average-Werte,
- eine vorsichtige Einschätzung: unauffällig, weiter beobachten oder
  genauer untersuchen.

Prüfe die Datei:

```bash
cat ~/servercheck.txt
```

## Zusammenfassung

Du kannst jetzt:

- Prozesse mit `ps` als Momentaufnahme untersuchen,
- mit `top` eine Live-Entwicklung beobachten,
- `htop` als optionale komfortable Alternative einordnen,
- Arbeitsspeicher mit `free -h` bewerten,
- Laufzeit und Load Average mit `uptime` lesen,
- Beobachtungen zu einer nachvollziehbaren Gesundheitsbewertung verbinden.

Im nächsten Modul untersuchst du gezielt einzelne Prozesse und ihre
Beziehungen.
