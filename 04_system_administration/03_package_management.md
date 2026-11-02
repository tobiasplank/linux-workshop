# Modul 4.3: Software installieren

## Anschluss an Modul 4.2

Auf einem Linux-Server wird Software nicht über einen heruntergeladenen
Installer installiert. Der Paketmanager `apt` bezieht geprüfte Pakete aus
Repositories, löst Abhängigkeiten auf und verwaltet Updates und Entfernung.

> [!CAUTION]
> `apt upgrade`, Installation und Entfernung verändern das System. Arbeite
> ausschließlich in der vorbereiteten, zurücksetzbaren Laborumgebung und
> verwende nur die freigegebenen Paketquellen und Aufgabenpakete – niemals ein
> persönliches oder produktives System.

<!-- pagebreak -->

## 1. Warum Paketverwaltung?

Prüfe, ob `ncdu` vorhanden ist:

```bash
command -v ncdu
```

Wenn keine Ausgabe erscheint, ist das Programm nicht installiert. `apt`
(Advanced Package Tool) kann Software installieren, aktualisieren und
entfernen. Es kümmert sich auch um benötigte Abhängigkeiten.

## 2. Paketlisten aktualisieren

Aktualisiere den lokalen Katalog:

```bash
sudo apt update
```

`apt update` installiert noch keine Software. Es lädt nur aktuelle
Informationen über Paketnamen, Versionen und Quellen. `Hit:` bedeutet, dass
eine Quelle bereits aktuell war; `Get:` zeigt neu geladene Informationen.

## 3. Installierte Software aktualisieren

Prüfe zuerst verfügbare Updates:

```bash
apt list --upgradable
```

Installiere anschließend die verfügbaren Aktualisierungen:

```bash
sudo apt upgrade -y
```

| Befehl | Wirkung |
| --- | --- |
| `apt update` | Paketkatalog aktualisieren |
| `apt upgrade` | installierte Pakete aktualisieren |

Die Reihenfolge ist wichtig: zuerst `update`, dann `upgrade`. Die Warnung
über die nicht stabile `apt`-CLI-Schnittstelle ist für die interaktive Nutzung
normal.

<!-- pagebreak -->

## 4. Was ist installiert?

Zeige alle installierten Pakete:

```bash
dpkg -l
```

`dpkg` ist das systemnahe Werkzeug, das `apt` im Hintergrund verwendet. Ein
Status `ii` bedeutet: Paket soll installiert sein und ist installiert.

Prüfe gezielt `nano` und `ncdu`:

```bash
dpkg -l | grep nano
dpkg -l | grep ncdu
```

Eine leere `grep`-Ausgabe bedeutet: kein Treffer. `nano` ist im Lab bereits
vorhanden, `ncdu` zunächst nicht.

## 5. Paketdetails nachschlagen

Zeige Metadaten und Abhängigkeiten:

```bash
apt show nano
apt show ncdu
```

Achte auf:

- `Description`: Zweck des Pakets
- `Version`: verfügbare Version
- `Installed-Size`: Platzbedarf nach Installation
- `Depends`: benötigte Abhängigkeiten

`apt show` liest Paketinformationen, installiert aber nichts.

## 6. Software installieren

Installiere `ncdu`:

```bash
sudo apt install ncdu -y
```

Prüfe:

```bash
ncdu --version
```

Starte die Speicheranalyse im eigenen Home-Verzeichnis:

```bash
ncdu ~
```

Navigiere mit den Pfeiltasten und öffne Verzeichnisse mit `Enter`. Beende
`ncdu` mit `q`. Ändere oder lösche in `ncdu` keine Dateien.

## 7. Software entfernen

Prüfe das vorbereitete Testpaket:

```bash
dpkg -l | grep cowsay
```

Entferne nur das Testpaket:

```bash
sudo apt remove cowsay -y
```

Prüfe:

```bash
dpkg -l | grep cowsay
```

Ein Status `rc` bedeutet: Programm entfernt, Konfigurationsdateien noch
vorhanden. `apt purge` würde zusätzlich diese Konfiguration entfernen:

| Befehl | Programm | Konfiguration |
| --- | --- | --- |
| `apt remove` | entfernt | behält sie |
| `apt purge` | entfernt | entfernt sie ebenfalls |

In diesem Lab reicht `remove`.

## 8. Fehlende Tools installieren

Lies die Wartungsliste:

```bash
cat ~/wartung/tools.txt
```

Die Liste enthält drei Toolnamen. Für jedes Tool:

1. Prüfe mit `command -v` oder `dpkg -l`, ob es installiert ist.
2. Installiere fehlende Tools mit `sudo apt install <paket> -y`.
3. Prüfe danach, ob der Befehl aufrufbar ist.

Beispiel für ein einzelnes Paket:

```bash
command -v werkzeug
dpkg -l | grep '^ii  werkzeug'
sudo apt install werkzeug -y
command -v werkzeug
```

Ersetze `werkzeug` durch den tatsächlichen Namen aus der Liste.

## Sicherheitsregeln

- Paketquellen und Paketnamen vor der Installation prüfen.
- `apt update` und `apt upgrade` nur in der vorbereiteten Laborumgebung
  durchführen.
- Keine unbekannten oder privaten Paketquellen hinzufügen.
- Keine Pakete außerhalb der Aufgabenliste installieren.
- Vor `remove` den exakten Paketnamen prüfen.
- Während `ncdu` keine Dateien löschen oder verändern.

## Zusammenfassung

Du kannst jetzt:

- den Paketkatalog mit `apt update` aktualisieren,
- verfügbare Updates prüfen und mit `apt upgrade` einspielen,
- installierte Pakete mit `dpkg` untersuchen,
- Paketdetails und Abhängigkeiten mit `apt show` lesen,
- fehlende Software mit `apt install` nachinstallieren,
- Pakete mit `apt remove` und `apt purge` unterscheiden,
- eine Tool-Liste systematisch abarbeiten.

Im nächsten Modul untersuchst du Speicherplatz und Dateisysteme gezielter.
