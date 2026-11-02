# Modul 3.2: Berechtigungen setzen

## Anschluss an Modul 3.1

Im letzten Lab hast du Berechtigungen mit `ls -l` gelesen. Jetzt änderst du
Dateirechte gezielt mit `chmod` (**change mode**). Du lernst die symbolische
Notation mit `u`, `g`, `o`, `a`, `+`, `-` und `=` sowie die oktale Notation
mit den Werten 4, 2 und 1.

> [!WARNING]
> Die Übungsdateien unter `~/uebungen/` sind für dieses Lab vorbereitet.
> Ändere ausschließlich diese Dateien und Verzeichnisse, prüfe jeden neuen
> Modus mit `stat` und verwende weder `sudo` noch echte Projektdateien.

<!-- pagebreak -->

## 1. Berechtigungen lesen

Verschaffe dir einen Überblick:

```bash
ls -l ~/uebungen/
```

Prüfe eine Datei zusätzlich mit `stat`. `%a` zeigt die oktale Zahl, `%A` den
symbolischen Berechtigungsstring und `%n` den Namen:

```bash
stat -c '%a %A %n' ~/uebungen/public.txt
```

Lies die Referenztabelle:

```bash
cat ~/referenz.txt
```

Beispiel: `644` und `-rw-r--r--` bedeuten dasselbe: Der Besitzer darf lesen
und schreiben, Gruppe und Andere dürfen nur lesen.

## 2. Symbolisch: Rechte hinzufügen

Prüfe zunächst das Script:

```bash
ls -l ~/uebungen/script.sh
```

Die Datei hat anfangs `-rw-r--r--`. Füge nur dem Besitzer das
Ausführungsrecht hinzu:

```bash
chmod u+x ~/uebungen/script.sh
```

Prüfe:

```bash
ls -l ~/uebungen/script.sh
```

`u` steht für User, `+` für Hinzufügen und `x` für Ausführen. Aus `644` wird
`744`: Die anderen Rechte bleiben unverändert.

<!-- pagebreak -->

## 3. Symbolisch: Rechte entfernen

`team-doc.txt` hat anfangs ungefähr `-rw-r-----` beziehungsweise `640`.
Entferne das Leserecht der Gruppe:

```bash
chmod g-r ~/uebungen/team-doc.txt
```

Prüfe:

```bash
stat -c '%a %A' ~/uebungen/team-doc.txt
```

Das Ergebnis ist `600 -rw-------`. `g` steht für Group und `-` für Entfernen.

## 4. Die oktale Notation verstehen

Die drei Ziffern stehen für User, Group und Other:

```text
chmod 7 5 5 datei
       │ │ │
       │ │ └── Other
       │ └──── Group
       └────── User
```

| Recht | Wert |
| ---: | --- |
| `r` | 4 |
| `w` | 2 |
| `x` | 1 |

Beispiele:

| Zahl | Rechte |
| ---: | --- |
| 7 | `rwx` |
| 6 | `rw-` |
| 5 | `r-x` |
| 4 | `r--` |
| 0 | `---` |

<!-- pagebreak -->

Prüfe die Dateien:

```bash
stat -c '%a %A %n' ~/uebungen/public.txt ~/uebungen/privat.txt ~/uebungen/script.sh
```

`public.txt` sollte `644` haben. `privat.txt` sollte `600` haben. Das Script
hat nach Aufgabe 2 `744`: Der Besitzer hat durch `u+x` den Wert 1
dazubekommen.

## 5. Oktal: Rechte exakt setzen

`public.txt` soll nur für dich lesbar und schreibbar sein. Berechne zuerst:

- User: Lesen + Schreiben = 4 + 2 = 6
- Group: keine Rechte = 0
- Other: keine Rechte = 0

Setze die Rechte exakt:

```bash
chmod 600 ~/uebungen/public.txt
```

Prüfe:

```bash
stat -c '%a %A' ~/uebungen/public.txt
```

Oktal setzt alle neun Rechte auf einmal. Symbolisch könntest du dieselbe
Änderung zum Beispiel mit `chmod go-r ~/uebungen/public.txt` erreichen,
wenn nur die Leserechte entfernt werden müssten.

## 6. Verzeichnisrechte

Bei Verzeichnissen bedeuten die Rechte:

| Recht | Bedeutung |
| --- | --- |
| `r` | Inhalt auflisten |
| `w` | Dateien erstellen oder löschen |
| `x` | Verzeichnis betreten und Dateien darin öffnen |

Prüfe das Verzeichnis:

```bash
stat -c '%a %A' ~/uebungen/archiv/
```

Es sollte `755 drwxr-xr-x` anzeigen. Sperre das Verzeichnis so, dass nur der
Besitzer es betreten, auflisten und verändern kann:

```bash
chmod 700 ~/uebungen/archiv/
```

Prüfe erneut:

```bash
stat -c '%a %A' ~/uebungen/archiv/
```

Das Verzeichnis `~/uebungen/geheim/` hat bereits den Modus 700. Prüfe das
ebenfalls:

```bash
stat -c '%a %A' ~/uebungen/geheim/
```

## 7. Zwei Notationen, ein Ergebnis

`~/uebungen/archiv/notizen.txt` hat anfangs `644`. Setze den Zielmodus
`754`: Besitzer darf lesen, schreiben und ausführen, die Gruppe lesen und
ausführen, Andere nur lesen.

Oktal:

```bash
chmod 754 ~/uebungen/archiv/notizen.txt
stat -c '%a %A' ~/uebungen/archiv/notizen.txt
```

Setze die Datei für den Vergleich zurück:

```bash
chmod 644 ~/uebungen/archiv/notizen.txt
```

Erzeuge dasselbe Ergebnis symbolisch:

```bash
chmod u+x,g+x ~/uebungen/archiv/notizen.txt
stat -c '%a %A' ~/uebungen/archiv/notizen.txt
```

Beide Wege ergeben `754 -rwxr-xr--`. Symbolisch änderst du gezielt einzelne
Rechte; oktal setzt du den vollständigen Modus.

## 8. Script ausführbar machen

Prüfe das vorbereitete Script:

```bash
ls -l ~/uebungen/archiv/backup.sh
```

Es hat anfangs `644`. Starte es einmal:

```bash
~/uebungen/archiv/backup.sh
```

<!-- pagebreak -->

`Permission denied` ist hier wegen des fehlenden Ausführungsrechts erwartet.
Setze den Modus so, dass Besitzer und Gruppe das Script ausführen dürfen,
Andere aber keinen Zugriff haben:

```bash
chmod 750 ~/uebungen/archiv/backup.sh
```

Prüfe und starte es erneut:

```bash
stat -c '%a %A' ~/uebungen/archiv/backup.sh
~/uebungen/archiv/backup.sh
```

## 9. Rechte nach Anforderungen setzen

Arbeite ohne vorgegebenen `chmod`-Befehl. Prüfe zuerst:

```bash
stat -c '%a %A' ~/uebungen/projekt-plan.txt ~/uebungen/notfall-kontakte.txt
```

Setze für `projekt-plan.txt` folgende Anforderungen:

- Besitzer: lesen, schreiben und ausführen
- Gruppe: lesen und ausführen, nicht schreiben
- Andere: keine Rechte

Setze für `notfall-kontakte.txt` folgende Anforderungen:

- Besitzer: nur lesen
- Gruppe: keine Rechte
- Andere: keine Rechte

Wähle selbst symbolische oder oktale Notation und prüfe:

```bash
stat -c '%a %A %n' ~/uebungen/projekt-plan.txt ~/uebungen/notfall-kontakte.txt
```

Die erwarteten Modi sind `750` für `projekt-plan.txt` und `400` für
`notfall-kontakte.txt`. Bei neu angelegten Dateien beeinflusst die `umask`
die Standardrechte. Das ist ein Thema für ein späteres Lab.

<!-- pagebreak -->

## Zusammenfassung

Du kannst jetzt:

- Berechtigungen mit `ls -l` und `stat` prüfen,
- mit `chmod u+x`, `chmod g-r` und ähnlichen Ausdrücken einzelne Rechte
  ändern,
- oktale Werte aus 4, 2 und 1 berechnen,
- mit `chmod 600` oder `chmod 750` einen vollständigen Modus setzen,
- Datei- und Verzeichnisrechte unterscheiden,
- ein Script kontrolliert ausführbar machen.

Im nächsten Modul wendest du das Berechtigungsmodell auf komplexere
Zugriffssituationen an.
