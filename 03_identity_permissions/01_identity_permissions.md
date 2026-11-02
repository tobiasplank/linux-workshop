# Modul 3.1: Identität und Berechtigungen

## Einordnung

In Modul 2 hast du dich im Terminal orientiert, Dateien angelegt, gelesen,
bearbeitet, gesucht und Ausgaben dokumentiert. Jetzt geht es um die nächste
Ebene: Benutzer, Gruppen und Dateiberechtigungen auf einem
Mehrbenutzersystem.

## Neuer Server, erste Fragen

Du hast Zugang zu einem Projektserver bekommen. Anna, Tom und Lisa arbeiten
gemeinsam an einer Kundenmanagement-Anwendung. Bevor du irgendetwas
veränderst, musst du den Server kennenlernen.

In diesem Lab lernst du:

- deine Identität mit `id` genauer zu untersuchen,
- die Benutzerdatenbank in `/etc/passwd` zu lesen,
- Gruppen in `/etc/group` zu verstehen,
- Besitzer, Gruppen und Berechtigungen mit `ls -l` zu lesen,
- das Modell **User, Group, Other** auf konkrete Dateien anzuwenden.

> [!WARNING]
> Die vorbereitete Laborumgebung enthält eigens für diese Übung angelegte
> Konten, Gruppen und Projektdateien. Lies die Daten nur; ändere weder
> `/etc/passwd`, `/etc/group`, `/etc/shadow` noch Dateien unter `/opt/projekt/`.
> Verwende kein `sudo`.

<!-- pagebreak -->

## 1. Mehr als ein Name: der id-Befehl

Prüfe zunächst, als wer du angemeldet bist:

```bash
whoami
```

Du solltest `student` sehen. Linux arbeitet intern aber nicht nur mit Namen,
sondern mit Nummern. Die UID (**User ID**) ist die eindeutige
Benutzer-ID. Die GID (**Group ID**) ist die Gruppen-ID deiner primären
Gruppe.

```bash
id
```

Die Ausgabe sieht ungefähr so aus:

```text
uid=1000(student) gid=1000(student) groups=1000(student),27(sudo)
```

| Feld | Bedeutung |
| --- | --- |
| `uid=1000(student)` | Benutzer-ID und Benutzername |
| `gid=1000(student)` | primäre Gruppen-ID und Gruppenname |
| `groups=...` | alle Gruppen, denen du angehörst |

Vergleiche `whoami` und `id`. `whoami` zeigt nur den Namen, `id` zeigt das
vollständige Bild deiner Identität und Gruppenzugehörigkeiten.

## 2. Wer arbeitet auf diesem Server?

Linux speichert Benutzerkonten in `/etc/passwd`. Passwörter stehen dort
nicht; ein `x` verweist auf die geschützte Datei `/etc/shadow`.

```bash
cat /etc/passwd
```

Eine Zeile hat dieses Format:

```text
anna:x:1001:1001:Anna Weber, Entwicklerin:/home/anna:/bin/bash
```

Die wichtigsten Felder sind:

1. Benutzername
2. UID
3. primäre GID
4. Kommentar, zum Beispiel Name oder Abteilung
5. Home-Verzeichnis
6. Login-Shell

Finde die Einträge für `anna`, `tom`, `lisa` und `student`. Ein Eintrag mit
`/bin/bash` ist ein interaktives Benutzerkonto. Ein Eintrag mit
`/usr/sbin/nologin` oder `/bin/false` gehört meistens zu einem Dienst und
kann sich nicht normal anmelden.

Beachte: UIDs und die Zahl der Konten können sich auf anderen Ubuntu-Systemen
unterscheiden. Lies die Ausgabe des tatsächlichen Labors.

## 3. Gruppen: gemeinsamer Zugriff

Zeige deine eigenen Gruppen:

```bash
groups
```

Vergleiche die Ausgabe mit `id`. `groups` zeigt vor allem die Namen,
`id` zusätzlich die Nummern.

Lies anschließend die Gruppendatenbank:

```bash
cat /etc/group
```

Suche die Zeilen für `entwicklung` und `design`. Sie sehen ungefähr so aus:

```text
entwicklung:x:1004:anna,tom
design:x:1005:lisa,anna
```

Die Felder bedeuten:

- Gruppenname
- Passwort-Platzhalter
- GID
- kommagetrennte Mitglieder

Anna ist in beiden Gruppen. Dadurch kann sie sowohl an Code als auch am
Design arbeiten. Gruppen vermeiden, dass Berechtigungen für jede Person und
für jede Datei einzeln gepflegt werden müssen.

<!-- pagebreak -->

## 4. Dateien unter der Lupe: ls -l

Das Projekt liegt unter `/opt/projekt/`. Lies die Details der Verzeichnisse:

```bash
ls -l /opt/projekt/
```

Ordne Besitzer und Gruppe zu:

| Verzeichnis | Besitzer | Gruppe |
| --- | --- | --- |
| `code/` | `anna` | `entwicklung` |
| `design/` | `lisa` | `design` |
| `shared/` | `root` | `root` |

Sieh dir auch die Dateien im gemeinsamen Bereich an:

```bash
ls -l /opt/projekt/shared/
```

Merke dir `db-credentials.conf`. Du untersuchst die Datei später erneut.

## 5. Berechtigungen entschlüsseln: rwx

Sieh dir eine Programmdatei an:

```bash
ls -l /opt/projekt/code/app.py
```

Ein Berechtigungsstring wie `-rwxr-x---` besteht aus zehn Zeichen:

```text
- rwx r-x ---
  │   │   └── Other: keine Rechte
  │   └────── Group: lesen und ausführen
  └────────── User: lesen, schreiben und ausführen
```

Das erste Zeichen beschreibt den Dateityp: `-` steht für eine normale Datei,
`d` für ein Verzeichnis. Danach folgen die Rechte für User, Group und Other.

| Zeichen | Datei | Verzeichnis |
| --- | --- | --- |
| `r` | Inhalt lesen | Inhalt auflisten |
| `w` | Inhalt ändern | Dateien erstellen oder löschen |
| `x` | Programm ausführen | Verzeichnis betreten |
| `-` | Recht fehlt | Recht fehlt |

<!-- pagebreak -->

Vergleiche eine Textdatei:

```bash
ls -l /opt/projekt/shared/readme.txt
```

Eine Textdatei muss normalerweise nicht ausführbar sein.

## 6. User, Group, Other

Untersuche die Aufgabenliste:

```bash
ls -l /opt/projekt/shared/todo.txt
```

Die Datei hat im Labor ungefähr `-rw-rw----`, gehört `tom` und der Gruppe
`entwicklung`. Überlege vor dem Test:

- Tom ist Besitzer und hat `rw-`.
- Anna ist Mitglied von `entwicklung` und hat `rw-`.
- Lisa ist weder Besitzerin noch Mitglied dieser Gruppe und hat `---`.
- `student` ist ebenfalls weder Besitzer noch Gruppenmitglied und hat `---`.

Teste deine Vorhersage:

```bash
cat /opt/projekt/shared/todo.txt
```

`Permission denied` ist hier kein Fehler der Übung, sondern der erwartete
Sicherheitsmechanismus. Vergleiche mit einer Datei, die für Other lesbar ist:

```bash
cat /opt/projekt/shared/readme.txt
```

Linux prüft die Kategorien in dieser Reihenfolge: zuerst User, dann Group,
sonst Other. Für den Besitzer gelten immer die User-Rechte, auch wenn er
zusätzlich Mitglied der Dateigruppe ist.

<!-- pagebreak -->

## 7. Verzeichnisrechte: x bedeutet eintreten

Prüfe die Projektverzeichnisse:

```bash
ls -l /opt/projekt/
```

`design/` hat im Labor ungefähr `drwxr-x---`. Für Other stehen keine Rechte.
Versuche deshalb:

```bash
cd /opt/projekt/design/
```

Das wird mit `Permission denied` scheitern. Auch dieser Versuch kann nicht
funktionieren:

```bash
ls /opt/projekt/design/
```

Ohne `x` darfst du das Verzeichnis nicht betreten. Wechsle anschließend in
den gemeinsamen Bereich:

```bash
cd /opt/projekt/shared/
pwd
```

`shared/` ist für Other mit `r-x` zugänglich. Wechsle am Ende zurück in dein
Home-Verzeichnis:

```bash
cd ~
```

## 8. Selbstständige Aufgabe: Serveranalyse

Erstelle den Ergebnisbericht nur an diesem ausdrücklich erlaubten Pfad:

```bash
nano /home/student/server-analyse.txt
```

Schreibe drei Zeilen:

1. Zähle in `/etc/passwd` alle Konten mit UID ab 1000 und `/bin/bash` als
   Shell. Schreibe nur die Zahl.
2. Schreibe die Mitglieder der Gruppe `entwicklung` kommagetrennt.
3. Schreibe den Berechtigungsstring von
   `/opt/projekt/shared/db-credentials.conf`.

Speichere mit `Strg+O`, bestätige mit `Enter` und beende mit `Strg+X`.
Prüfe den Bericht:

```bash
cat /home/student/server-analyse.txt
```

Verwende nur Informationen aus den tatsächlich gelesenen Dateien. Verändere
keine Datei unter `/etc` oder `/opt/projekt`.

## Zusammenfassung

Du kannst jetzt:

- deine Identität mit `id` beschreiben,
- Benutzer in `/etc/passwd` unterscheiden,
- Gruppen in `/etc/group` lesen,
- `ls -l`-Ausgaben in Besitzer, Gruppe und Rechte zerlegen,
- `r`, `w` und `x` bei Dateien und Verzeichnissen erklären,
- `Permission denied` als Folge des Berechtigungsmodells einordnen.

Im nächsten Modul setzt du Berechtigungen selbst und beobachtest die
Auswirkungen kontrolliert im eigenen Arbeitsbereich.
