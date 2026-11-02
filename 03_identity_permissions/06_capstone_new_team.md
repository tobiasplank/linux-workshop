# Modul 3.6: Capstone – Neues Team

## Abschluss des Themenbereichs

In den Modulen 3.1 bis 3.5 hast du Benutzeridentitäten, Gruppen, Besitz,
Berechtigungen, `sudo` und die systematische Fehlersuche kennengelernt. Jetzt
verbindest du alles in einem realistischen Team-Setup-Auftrag.

Ein neues Team übernimmt den Projektserver. Die Ausgangslage ist absichtlich
unvollständig und teilweise falsch. Du arbeitest wie eine
Systemadministration: erst den Zustand aufnehmen, dann gezielt reparieren,
abschließend prüfen und dokumentieren.

> [!CAUTION]
> Dieses Capstone verändert Benutzerkonten, Gruppen und `/opt/projekt/`.
> Arbeite ausschließlich in der vorbereiteten, isolierten und
> zurücksetzbaren Laborumgebung. Verwende keine echten Passwörter und ändere
> keine anderen Systempfade. Das offene Modusziel `777` für `shared/` ist
> ausschließlich ein bewusstes Laborziel, kein Produktionsstandard.

<!-- pagebreak -->

## 1. Bestandsaufnahme

Lies das Szenario-Dokument:

```bash
cat ~/aufgaben/szenario.txt
```

Verschaffe dir anschließend einen Überblick:

```bash
whoami
groups
cat /etc/passwd
cat /etc/group
ls -la /opt/projekt/
find /opt/projekt/ -maxdepth 2 -exec ls -ld {} \;
```

Erstelle eine knappe Arbeitsnotiz:

```bash
nano ~/analyse.txt
```

Notiere mindestens:

- welche von `anna`, `tom` und `lisa` fehlen oder unvollständig sind,
- welche Gruppen fehlen,
- was bei Besitz oder Berechtigungen unter `/opt/projekt/` auffällig ist.

Prüfe die Mindestanforderungen:

```bash
wc -l ~/analyse.txt
cat ~/analyse.txt
```

## 2. Benutzer einrichten

Richte für `anna`, `tom` und `lisa` vollständige Konten ein. Prüfe vor jedem
Anlegen, ob der Benutzer bereits existiert:

```bash
id anna
id tom
id lisa
```

Fehlende Konten legst du mit Home-Verzeichnis und Bash an:

```bash
sudo useradd -m -s /bin/bash anna
sudo useradd -m -s /bin/bash tom
sudo useradd -m -s /bin/bash lisa
```

<!-- pagebreak -->

Falls ein Konto bereits existiert, verwende nicht erneut `useradd`. Repariere
stattdessen die fehlenden Eigenschaften:

```bash
sudo usermod -s /bin/bash anna
sudo usermod -s /bin/bash tom
sudo usermod -s /bin/bash lisa
```

Prüfe Home-Verzeichnisse und Shells:

```bash
for user in anna tom lisa; do
    getent passwd "$user"
    ls -ld "/home/$user"
done
```

Setze bei Bedarf für die Laboraccounts ein nicht-produktives Testpasswort:

```bash
sudo passwd anna
sudo passwd tom
sudo passwd lisa
```

## 3. Gruppen erstellen und zuordnen

Prüfe zuerst, ob die Gruppen bereits existieren:

```bash
getent group entwicklung
getent group design
```

Erstelle fehlende Gruppen:

```bash
sudo groupadd entwicklung
sudo groupadd design
```

Ordne die Benutzer zusätzlich zu ihren persönlichen Gruppen zu:

```bash
sudo usermod -aG entwicklung anna
sudo usermod -aG design anna
sudo usermod -aG entwicklung tom
sudo usermod -aG design lisa
```

Das `-a` in `-aG` ist wichtig: Die zusätzlichen Gruppen werden ergänzt und
nicht ersetzt. Prüfe:

```bash
groups anna
groups tom
groups lisa
```

Zielzustand:

- Anna: `entwicklung` und `design`
- Tom: `entwicklung`
- Lisa: `design`

## 4. Kaputte Ausgangslage reparieren

Prüfe die Projektverzeichnisse:

```bash
find /opt/projekt/code/ /opt/projekt/design/ -maxdepth 2 -exec ls -ld {} \;
```

Setze die Gruppenzuordnung rekursiv:

```bash
sudo chgrp -R entwicklung /opt/projekt/code/
sudo chgrp -R design /opt/projekt/design/
```

Prüfe die Ergebnisse:

```bash
find /opt/projekt/code/ /opt/projekt/design/ -maxdepth 2 -exec ls -ld {} \;
```

Die Gruppenänderung muss für die Verzeichnisse und alle darin enthaltenen
Dateien gelten.

## 5. Projektstruktur vervollständigen

Erstelle das fehlende gemeinsame Verzeichnis:

```bash
sudo mkdir -p /opt/projekt/shared/
```

Setze einen neutralen Besitzer:

```bash
sudo chown root:root /opt/projekt/shared/
```

Prüfe:

```bash
ls -ld /opt/projekt/shared/
```

Der endgültige Zugriff wird im nächsten Schritt gesetzt. Das gemeinsame
Verzeichnis muss beiden Teams offenstehen; im folgenden Laborziel wird dies
bewusst über `Other` umgesetzt.

## 6. Zugriffsrechte setzen

Setze die Rechte für die beiden Teamverzeichnisse:

```bash
sudo chmod 770 /opt/projekt/code/
sudo chmod 770 /opt/projekt/design/
```

Setze den ausdrücklich geforderten Laborzielzustand für den gemeinsamen
Bereich:

```bash
sudo chmod 777 /opt/projekt/shared/
```

`777` bedeutet `rwx` für User, Group und Other. Das ist für ein echtes
Produktivsystem meist zu offen, wird hier aber als bewusstes, überprüfbares
Capstone-Ziel verwendet, damit beide getrennten Teams ohne zusätzliche
Gruppenkonstruktion zugreifen können.

Prüfe alle drei Verzeichnisse:

```bash
ls -ld /opt/projekt/code/ /opt/projekt/design/ /opt/projekt/shared/
```

Erwartet werden `770`, `770` und `777`.

## 7. Abschlussdokumentation

Erstelle die Übergabe:

```bash
nano ~/doku.txt
```

Dokumentiere mindestens:

- Anna, Tom und Lisa,
- die Gruppen `entwicklung` und `design`,
- die Projektstruktur unter `/opt/projekt/`,
- die Modi und Gruppen von `code/`, `design/` und `shared/`.

Prüfe Umfang und Inhalt:

```bash
wc -l ~/doku.txt
cat ~/doku.txt
```

<!-- pagebreak -->

## Abschlusskontrolle

Führe die wichtigsten Prüfungen erneut aus:

```bash
for user in anna tom lisa; do
    id "$user"
    getent passwd "$user" | cut -d: -f1,6,7
done

getent group entwicklung
getent group design
find /opt/projekt/code/ /opt/projekt/design/ -maxdepth 1 -exec ls -ld {} \;
ls -ld /opt/projekt/shared/
```

Das Capstone ist abgeschlossen, wenn:

- `~/analyse.txt` mindestens drei sinnvolle Zeilen enthält,
- alle drei Benutzer ein Home und `/bin/bash` haben,
- die Gruppenmitgliedschaften dem Zielzustand entsprechen,
- `code/` rekursiv der Gruppe `entwicklung` gehört,
- `design/` rekursiv der Gruppe `design` gehört,
- `shared/` existiert und Modus `777` hat,
- `code/` und `design/` Modus `770` haben,
- `~/doku.txt` mindestens acht verständliche Zeilen enthält.

## Zusammenfassung

Du hast einen absichtlich fehlerhaften Teamserver untersucht, Konten
vervollständigt, Gruppen eingerichtet, Besitz rekursiv korrigiert, eine
Projektstruktur ergänzt, Zugriffsrechte gesetzt und den Zielzustand
dokumentiert. Damit hast du den Themenbereich Identität und Berechtigungen
als zusammenhängenden Administrationsprozess abgeschlossen.
