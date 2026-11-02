# Modul 3.5: Fehler finden und beheben

## Anschluss an Modul 3.4

In den letzten Labs hast du Benutzer, Gruppen, Besitz und Berechtigungen
verwaltet. Jetzt arbeitest du wie im IT-Support: Du bekommst ein Symptom,
untersuchst den Ist-Zustand, grenzt die Ursache ein, behebst nur die Ursache
und testest das Ergebnis.

> [!CAUTION]
> Dieses Lab verändert vorbereitete Systemzustände. Arbeite ausschließlich
> auf dem dedizierten, zurücksetzbaren Labor-Laptop oder in der Labor-VM.
> Verwende `sudo` nur für die genannten Pfade und korrigiere nur das
> erforderliche Recht oder die erforderliche Mitgliedschaft.

<!-- pagebreak -->

## 1. Problemliste lesen

Lies zuerst alle Tickets:

```bash
cat ~/aufgaben/problemliste.txt
```

Prüfe danach den Ausgangszustand:

```bash
whoami
groups
id anna
id tom
```

Erstelle eine Statusdatei:

```bash
printf 'Benutzer: ' > ~/aufgaben/status.txt
whoami >> ~/aufgaben/status.txt
printf 'Gruppen: ' >> ~/aufgaben/status.txt
groups >> ~/aufgaben/status.txt
printf 'Anna: ' >> ~/aufgaben/status.txt
id anna >> ~/aufgaben/status.txt
printf 'Tom: ' >> ~/aufgaben/status.txt
id tom >> ~/aufgaben/status.txt
```

Der sinnvolle Ablauf für jedes Ticket ist:

1. Meldung lesen
2. Ist-Zustand prüfen
3. Ursache eingrenzen
4. minimalen Fix anwenden
5. Ergebnis testen

<!-- pagebreak -->

## 2. Problem 1: Datei nicht lesbar

Die Meldung lautet:

> Ich kann `/opt/projekt/bericht.txt` nicht lesen. Ich bekomme `Permission
> denied`.

Untersuche zuerst Besitz und Rechte:

```bash
ls -l /opt/projekt/bericht.txt
id
```

Behebe die Ursache mit der kleinsten sinnvollen Änderung. Der Bericht soll
für alle Mitarbeiter lesbar sein. Setze nicht pauschal Schreib- oder
Ausführungsrechte:

```bash
sudo chmod o+r /opt/projekt/bericht.txt
```

Prüfe:

```bash
ls -l /opt/projekt/bericht.txt
cat /opt/projekt/bericht.txt
```

Falls die Rechte oder der fachliche Zielzustand in deiner Laborumgebung
abweichen, leite den Fix aus dem tatsächlichen Besitzer-, Gruppen- und
Berechtigungsmodell ab.

## 3. Problem 2: Verzeichnis nicht betretbar

Die Meldung lautet:

> Ich sehe mit `ls` Dateien in `/opt/archiv/`, aber `cd /opt/archiv/`
> ergibt `Permission denied`.

Prüfe die Verzeichnisrechte:

```bash
ls -ld /opt/archiv/
ls /opt/archiv/
```

`r` erlaubt das Auflisten, `x` das Betreten und den Zugriff auf Einträge.
Wenn nur `x` fehlt, ergänze es für die passende Zugriffsklasse. Für einen
allgemein zugänglichen Archivbereich gilt im vorbereiteten Lab:

```bash
sudo chmod o+x /opt/archiv/
```

<!-- pagebreak -->

Teste:

```bash
cd /opt/archiv/
pwd
cd ~
```

Verwende nicht automatisch `chmod 777`; ändere nur das notwendige Recht.

## 4. Problem 3: Script nicht ausführbar

Die Meldung lautet:

> `./scripts/backup.sh` endet mit `Permission denied`.

Das Script liegt in deinem Home-Verzeichnis. Prüfe:

```bash
ls -l ~/scripts/backup.sh
```

Du bist der Besitzer. Ergänze nur dein Ausführungsrecht:

```bash
chmod u+x ~/scripts/backup.sh
```

Prüfe und starte das Script:

```bash
ls -l ~/scripts/backup.sh
~/scripts/backup.sh
```

Für diese Änderung brauchst du kein `sudo`, weil die Datei dir gehört.

## 5. Problem 4: Zugriff trotz Gruppenverzeichnis verweigert

Die Meldung lautet:

> Tom soll auf `/opt/support/` zugreifen, bekommt aber `Permission denied`.

Untersuche Benutzer, Gruppe und Verzeichnis:

```bash
id tom
ls -ld /opt/support/
getent group support
```

Füge Tom der bestehenden Gruppe `support` hinzu. Das `-a` verhindert, dass
seine bisherigen zusätzlichen Gruppen ersetzt werden:

```bash
sudo usermod -aG support tom
```

Prüfe:

```bash
groups tom
sudo -u tom ls /opt/support/
```

Falls der Gruppenmodus des Verzeichnisses kein `x` enthält, muss zusätzlich
das fehlende Gruppenrecht gesetzt werden. Im vorbereiteten Fall ist nur die
Gruppenzuordnung defekt. Verändere den Besitzer nicht ohne Begründung.

## 6. Problem 5: Besitzerwechsel schlägt fehl

Die Meldung lautet:

> Ich kann den Besitzer von `/opt/shared/wichtig.txt` nicht ändern.

Prüfe den Ausgangszustand:

```bash
ls -l /opt/shared/wichtig.txt
```

Ein normaler Benutzer darf den Besitzer einer Datei nicht beliebig ändern.
Übernimm die Datei mit einem einzelnen privilegierten Befehl:

```bash
sudo chown student:student /opt/shared/wichtig.txt
```

Prüfe:

```bash
ls -l /opt/shared/wichtig.txt
```

Erkläre im Abschlussbericht, warum der erste Versuch ohne `sudo` scheitert.

## 7. Problem 6: Benutzer ohne Home-Verzeichnis

Die Meldung lautet:

> Der Praktikant wurde angelegt, landet beim Anmelden aber in `/` und kann
> keine Dateien in seinem Home ablegen.

Prüfe Konto und Home-Verzeichnis:

```bash
id praktikant
grep '^praktikant:' /etc/passwd
ls -ld /home/praktikant
```

Erstelle das fehlende Verzeichnis und setze den passenden Besitz:

```bash
sudo mkdir -p /home/praktikant
sudo chown praktikant:praktikant /home/praktikant
sudo chmod 700 /home/praktikant
```

Prüfe:

```bash
ls -ld /home/praktikant
```

Das Home-Verzeichnis soll dem Benutzer gehören und für andere Benutzer
gesperrt sein.

## 8. Incident-Bericht schreiben

Dokumentiere die sechs Fälle in:

```bash
nano ~/aufgaben/abschlussbericht.txt
```

Für jedes Problem gehören mindestens diese drei Informationen hinein:

- gemeldetes Symptom,
- tatsächliche Ursache,
- verwendeter Fix und anschließender Test.

Der Bericht muss mindestens zwölf Zeilen umfassen und alle sechs Probleme
abdecken. Prüfe ihn:

```bash
cat ~/aufgaben/abschlussbericht.txt
```

## Sicherheitsregeln

- Vor jedem Fix den Ist-Zustand mit `ls`, `id`, `getent` oder `grep` prüfen.
- Nur das notwendige Recht oder die notwendige Mitgliedschaft ändern.
- Kein `chmod 777`, kein `chown -R` und keine Änderungen außerhalb der
  genannten Pfade.
- `sudo` nur für die Fälle verwenden, die System- oder fremde Dateien
  betreffen.
- Die Übung ausschließlich auf dem vorbereiteten Laborgerät durchführen.

## Zusammenfassung

Du kannst jetzt Support-Tickets strukturiert bearbeiten: beobachten,
diagnostizieren, minimal korrigieren und verifizieren. Du erkennst dabei
fehlende Lese-, Betretungs- und Ausführungsrechte, fehlende
Gruppenmitgliedschaften, privilegierte Besitzwechsel und fehlende
Home-Verzeichnisse.
