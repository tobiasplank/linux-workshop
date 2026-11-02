# Modul 3.4: Benutzer und Gruppen verwalten

## Anschluss an Modul 3.3

In Modul 3.3 hast du Besitzer und Gruppen mit `chown`, `chgrp` und `sudo`
verwaltet. Jetzt richtest du neue Benutzerkonten und Teamgruppen ein und
verbindest sie mit einem gemeinsamen Projektverzeichnis.

> [!CAUTION]
> Dieses Lab verändert Benutzerkonten, Gruppen, Passwörter und
> `/opt/projekte/`. Arbeite ausschließlich in der vorbereiteten, isolierten
> Labor-VM oder auf einem dafür bereitgestellten und zurücksetzbaren
> Labor-Laptop – niemals auf einem persönlichen Ubuntu-Laptop oder
> Produktivserver. Verwende keine echten oder wiederverwendeten Passwörter.

<!-- pagebreak -->

## 1. Die Anforderungen lesen

Lies die Mitarbeiterliste:

```bash
cat ~/aufgaben/anforderungen.txt
```

Lies anschließend den Gruppenplan:

```bash
cat ~/aufgaben/gruppen-plan.txt
```

Merke dir, wer zu welcher Abteilung gehört. Eine Person arbeitet in zwei
Gruppen.

## 2. Den ersten Benutzer anlegen

`useradd` erstellt Benutzerkonten. Die beiden wichtigen Optionen sind:

| Option | Wirkung |
| --- | --- |
| `-m` | erstellt `/home/benutzername` |
| `-s /bin/bash` | setzt Bash als Login-Shell |

Lege Anna an:

```bash
sudo useradd -m -s /bin/bash anna
```

Prüfe Konto und Home-Verzeichnis:

```bash
id anna
ls /home/
```

Die eigene Gruppe `anna` wird bei der verwendeten Ubuntu-Konfiguration
automatisch mit angelegt.

## 3. Ein Passwort setzen

Setze ein Passwort für Anna:

```bash
sudo passwd anna
```

Das Passwort wird zweimal eingegeben. Während der Eingabe erscheinen keine
Zeichen. Verwende ausschließlich ein einfaches, nur für diese Labor-VM
gedachtes Testpasswort und niemals ein persönliches oder produktives
Passwort.

<!-- pagebreak -->

## 4. Zwei weitere Benutzer anlegen

Lege Tom vollständig an:

```bash
sudo useradd -m -s /bin/bash tom
sudo passwd tom
```

Lege Lisa absichtlich ohne `-s /bin/bash` an:

```bash
sudo useradd -m lisa
sudo passwd lisa
```

Vergleiche die Shells:

```bash
grep -E '^(anna|tom|lisa):' /etc/passwd
```

Lisa hat zunächst wahrscheinlich `/bin/sh`, während Anna und Tom
`/bin/bash` verwenden. Das fehlende Flag hat ein funktionierendes, aber
anders konfiguriertes Konto erzeugt.

## 5. Lisas Shell reparieren

`usermod` ändert Eigenschaften eines vorhandenen Kontos. Setze Lisas
Login-Shell:

```bash
sudo usermod -s /bin/bash lisa
```

Kontrolliere:

```bash
grep '^lisa:' /etc/passwd
```

Die Änderung wird beim nächsten Login wirksam.

## 6. Gruppen erstellen

Erstelle die Teamgruppen:

```bash
sudo groupadd entwicklung
sudo groupadd design
```

Prüfe eine Gruppe:

```bash
grep '^entwicklung:' /etc/group
```

Die Mitgliederliste kann anfangs noch leer sein.

## 7. Benutzer den Gruppen zuordnen

Verwende `usermod -aG`:

```bash
sudo usermod -aG entwicklung anna
sudo usermod -aG entwicklung tom
sudo usermod -aG design lisa
sudo usermod -aG design anna
```

Das `-a` bedeutet **append**, also hinzufügen. Ohne `-a` würde `-G` die
bisherige zusätzliche Gruppenliste ersetzen. Prüfe:

```bash
groups anna
groups tom
groups lisa
```

Erwartet wird:

- Anna: persönliche Gruppe `anna`, `entwicklung` und `design`
- Tom: persönliche Gruppe `tom` und `entwicklung`
- Lisa: persönliche Gruppe `lisa` und `design`

## 8. Projektverzeichnis einrichten

Das vorbereitete Verzeichnis gehört zunächst root:

```bash
ls -ld /opt/projekte/
```

Gib der Gruppe `entwicklung` den Besitz und setze den gemeinsamen Zugriff:

```bash
sudo chgrp entwicklung /opt/projekte/
sudo chmod 770 /opt/projekte/
```

Prüfe:

```bash
ls -ld /opt/projekte/
```

Erwartet wird ungefähr `drwxrwx--- root entwicklung`. Anna und Tom können
hineinwechseln und dort arbeiten. Lisa kann es nicht, weil sie nicht in
`entwicklung` ist.

<!-- pagebreak -->

## 9. Neuer Mitarbeiter: Max

Max Bauer verstärkt die Entwicklungsabteilung. Richte sein Konto selbständig
ein. Denke an die drei notwendigen Schritte:

1. Konto mit Home-Verzeichnis und Bash anlegen
2. nur für die Laborumgebung ein Testpasswort setzen
3. Max der Gruppe `entwicklung` zuordnen

Verwende die passenden Befehle aus den vorherigen Aufgaben. Prüfe:

```bash
id max
groups max
```

Max soll dieselbe Teamzugehörigkeit wie Tom haben und auf
`/opt/projekte/` zugreifen können.

## Sicherheitsregeln

- Dieses Lab nur in einer vorbereiteten isolierten VM oder auf einem
  zurücksetzbaren Labor-Laptop ausführen.
- Keine echten Passwörter verwenden oder wiederverwenden.
- Keine fremden oder produktiven Benutzerkonten anlegen.
- Vor `useradd`, `groupadd`, `usermod`, `chgrp` und `chmod` die Zielnamen
  prüfen.
- Keine Konten oder Gruppen löschen.
- Keine Änderungen an anderen Verzeichnissen als dem ausdrücklich genannten
  `/opt/projekte/`.

## Zusammenfassung

Du kannst jetzt:

- Anforderungen aus Dateien in konkrete Administrationsschritte übersetzen,
- Benutzer mit Home-Verzeichnis und Shell anlegen,
- Passwörter sicher eingeben,
- Kontoeigenschaften mit `usermod` ändern,
- Gruppen erstellen,
- Benutzer mit `usermod -aG` mehreren Teams zuordnen,
- einen Gruppenarbeitsbereich mit `chgrp` und `chmod` einrichten.

Damit ist die Grundlage geschaffen, Zugriffe über Teamgruppen statt über
Einzelpersonen zu organisieren.
