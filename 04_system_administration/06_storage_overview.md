# Modul 4.6: Speicher & Überblick

## Anschluss an die bisherigen Module

Ein Monitoring meldet ein Speicherproblem. Du untersuchst zuerst den
Gesamtüberblick und suchst danach die tatsächlichen Speicherfresser in deinem
Arbeitsbereich. Dieses Modul übt Diagnose und gezieltes Aufräumen; der
abschließende Rückblick auf den Themenbereich folgt erst in Modul 4.7.

> [!CAUTION]
> Lösche ausschließlich die vier vorbereiteten, eindeutig identifizierten
> Dateien unter `~`. Prüfe jede Datei vor `rm`; verwende keine
> Wildcard-Löschung und kein `sudo`.

<!-- pagebreak -->

## 1. Die Warnung

Prüfe den Speicherüberblick:

```bash
df -h
```

`df` (disk free) zeigt die Belegung eingehängter Dateisysteme. In diesem Lab
läuft der Server in einem Container. Deshalb zeigt `df -h` vor allem die
Host-Festplatte. Die Zeile für `/` kann unauffällig aussehen, obwohl dein
Home-Verzeichnis bereits große Dateien enthält.

Auf echten Servern sind volle Dateisysteme kritisch: Logs können nicht mehr
geschrieben werden, Datenbanken keine neuen Einträge annehmen und Updates
fehlschlagen. In diesem Lab ist `df` trotzdem der richtige erste Überblick.
Für die Suche nach der Ursache verwendest du anschließend `du`.

## 2. Festplattenüberblick mit df

Vergleiche die Ausgabe ohne und mit lesbaren Größen:

```bash
df
df -h
```

Die wichtigsten Spalten von `df -h` sind:

| Spalte | Bedeutung |
| --- | --- |
| `Filesystem` | Dateisystem oder Partition |
| `Size` | Gesamtgröße |
| `Used` | belegter Speicher |
| `Avail` | verfügbarer Speicher |
| `Use%` | Belegung in Prozent |
| `Mounted on` | Einhängepunkt |

Suche die Zeile mit `Mounted on` gleich `/`. Prozentwerte und Größen können
je nach Container und System stark abweichen.

<!-- pagebreak -->

## 3. Wo ist der Platz? Mit du

`du` (disk usage) misst die tatsächliche Größe von Verzeichnissen und Dateien.
Prüfe zunächst dein Home:

```bash
du -sh ~
```

`-s` zeigt nur die Summe, `-h` verwendet lesbare Einheiten. Untersuche danach
die vorbereiteten Verzeichnisse:

```bash
du -sh ~/projekte
du -sh ~/temp
du -sh ~/dokumente
```

Im vorbereiteten Zustand liegt der Home-Verbrauch ungefähr bei 180 MB. Der
konkrete Wert kann abweichen. Entscheidend ist, welches Verzeichnis deutlich
größer ist als die anderen.

## 4. Die größten zuerst

Wechsle in dein Home und sortiere die Einträge:

```bash
cd ~
du -sh *
du -sh * | sort -h
du -sh * | sort -rh
```

Die Pipe (`|`) übergibt die Ausgabe von `du` an `sort`. `sort -h` versteht
Einheiten wie `K`, `M` und `G`; `-r` stellt die größten Einträge nach oben.

Wechsle anschließend in das größte Verzeichnis und wiederhole die Suche:

```bash
cd ~/projekte
du -sh * | sort -rh
```

Arbeite dich so schrittweise bis zu den konkreten Dateien vor.

<!-- pagebreak -->

## 5. Aufräumen

Finde die vier größten unnötigen Dateien in deinem Home-Verzeichnis. Prüfe
vor jedem Löschen, ob die Datei wirklich zu den vorbereiteten Altlasten
gehört. Verwende dazu unter anderem:

```bash
ls -a ~
du -sh ~/.cache/*
find ~ -type f -size +20M
```

`ls -a` zeigt auch versteckte Einträge. `find` sucht rekursiv; `-type f`
beschränkt die Treffer auf Dateien und `-size +20M` auf Dateien größer als
20 MB.

Lösche anschließend ausschließlich die vier eindeutig identifizierten,
vorbereiteten Dateien:

```bash
rm -- /absoluter/pfad/zur/ersten-datei
rm -- /absoluter/pfad/zur/zweiten-datei
rm -- /absoluter/pfad/zur/dritten-datei
rm -- /absoluter/pfad/zur/vierten-datei
```

Ersetze die Beispielpfade durch die tatsächlich geprüften Dateien. `rm`
löscht ohne Papierkorb. Verwende keine ungeprüfte Wildcard und lösche nichts
außerhalb deines Home-Verzeichnisses.

## 6. Ergebnis prüfen

Belege die Wirkung deiner Aufräumaktion aus drei Blickwinkeln:

```bash
du -sh ~
find ~ -type f -size +20M
df -h
```

Das Home-Verzeichnis sollte nach dem Löschen nur noch wenige Megabyte
umfassen, abhängig vom vorbereiteten Inhalt. Die vier alten großen Dateien
dürfen bei `find` nicht mehr erscheinen. Bei `df -h` kann sich im Container
kaum etwas ändern; für dieses Lab sind `du` und `find` die aussagekräftigeren
Nachweise.

<!-- pagebreak -->

## Sicherheitsregeln

- Prüfe jede Datei vor dem Löschen.
- Lösche nur die vier vorbereiteten Altlasten unter `~`.
- Verwende kein `sudo` und ändere nichts außerhalb deines Home-Verzeichnisses.
- `rm` ist unwiderruflich; wichtige Projekt- oder Systemdateien werden nicht
  gelöscht.

## Überleitung

Du hast Speicherwarnungen mit `df` eingeordnet, die Ursache mit `du`,
`sort` und `find` eingegrenzt und die Wirkung eines gezielten Aufräumens
überprüft. Im nächsten Modul werden die bisherigen Administrationsaufgaben
zusammengeführt.
