
# Docker Aufgaben

## Docker – erster Container und Aufräumen

### Ziel der Aufgabe

Ziel war es, den ersten Docker Container zu starten, die dabei entstehenden Spuren zu untersuchen und anschliessend wieder vollständig zu entfernen.

---

### 1. Erster Container starten

Der erste Testcontainer wurde mit folgendem Befehl gestartet:

```bash
docker run hello-world
```

Dieser Container lädt ein Test-Image, gibt eine Nachricht aus und beendet sich danach automatisch.

---

### 2. Überprüfung laufender Container

Um zu prüfen, ob der Container noch läuft, wurde folgender Befehl verwendet:

```bash
docker ps
```

Der Container war nicht mehr sichtbar, da er sich direkt nach der Ausführung beendet hat.

---

### 3. Alle Container anzeigen

Um auch gestoppte Container zu sehen, wurde verwendet:

```bash
docker ps -a
```

Hier wurden folgende Informationen sichtbar:

- Container ID
- Image (hello-world)
- Status (exited)

---

### 4. Images anzeigen

Zur Identifikation der Images wurde folgender Befehl genutzt:

```bash
docker images
```

Dabei wurde das Image `hello-world` mit einer Image ID angezeigt.

---

### 5. Container und Image löschen

Zuerst wurde der Container gelöscht:

```bash
docker rm 7bdd7c959274
```

Danach wurde das Image entfernt:

```bash
docker rmi 6dc565aa6309
```

### Problem

Beim Löschen des Images trat folgender Fehler auf:

```Fehler
unable to delete image: image is being used by stopped container
```

Lösung:

1. Alle Container löschen die mit diesem Image verbunden sind
2. Danach Image löschen

---

### 6. Ergebnis nach Problembehandlung

Nach dem beheben waren:

- keine hello-world Container mehr vorhanden
- kein hello-world Image mehr vorhanden

---

### Fazit

- Ein Docker Container wurde erfolgreich gestartet
- Container und Image wurden korrekt identifiziert
- Alle Spuren wurden anschliessend wieder entfernt
- Docker Befehle `ps`, `ps -a`, `images`, `rm` und `rmi` wurden verwendet

## Docker ohne sudo verwenden

Standardmässig erfordern Docker-Befehle unter Linux erhöhte Rechte (`sudo`). Um dies zu umgehen, kann der Benutzer der Docker-Gruppe hinzugefügt werden.

---

### Schritt 1: Benutzer zur Docker-Gruppe hinzufügen

```bash
sudo usermod -aG docker $USER
```

Dieser Befehl fügt den aktuellen Benutzer der Gruppe `docker` hinzu, wodurch Docker-Befehle ohne `sudo` ausgeführt werden können.

---

### Schritt 2: Änderungen aktivieren

Die neue Gruppenzugehörigkeit kann auf zwei Wegen aktiviert werden:

```bash
newgrp docker
```

oder durch einen Neustart des Terminals.

---

### Schritt 3: Überprüfung

Nach der Aktivierung können Docker-Befehle ohne `sudo` ausgeführt werden:

```bash
docker ps
docker run hello-world
```

---

### Ergebnis

Durch das Hinzufügen zur Docker-Gruppe können Docker-Befehle direkt ohne erhöhte Rechte verwendet werden.

## Docker Image auf Docker Hub veröffentlichen

### Ziel der Aufgabe

Ein lokal erstelltes Docker Image (`pythonwebserver`) wird auf Docker Hub veröffentlicht und somit öffentlich zugänglich gemacht.

---

### Vorgehen

### 1. Docker Image lokal erstellen

Das Image wird mit folgendem Befehl gebaut:

```bash
docker build -f Dockerfile . -t pythonwebserver
```

### 2. Image überprüfen

```bash
docker images
```

Das Image `pythonwebserver:latest` ist danach lokal vorhanden.

### 3. Image für Docker Hub taggen

Das Image wird mit dem Docker Hub Repository verknüpft:

```bash
docker tag pythonwebserver z8o49/pythonwebserver
```

### 4. Login bei Docker Hub

```bash
docker login
```

Die Anmeldung erfolgt über den Browser (Device Login) und wird dort bestätigt.

### 5. Image auf Docker Hub hochladen

```bash
docker push z8o49/pythonwebserver
```

Das Image wird hochgeladen und alle Layer werden übertragen.

---

### Ergebnis

Das Docker Image ist nun öffentlich auf Docker Hub verfügbar.

Repository: https://hub.docker.com/r/z8o49/pythonwebserver

---

### Zusammenfassung

- Docker Image lokal erstellt
- Image korrekt getaggt für Docker Hub
- Erfolgreich bei Docker Hub eingeloggt
- Image erfolgreich gepusht
- Öffentliches Repository erstellt

---

### Fazit

Das Docker Image kann nun weltweit mit folgendem Befehl ausgeführt werden:

```bash
docker run z8o49/pythonwebserver
```

## Container Performance

### Ziel der Aufgabe

Es soll überprüft werden, wie viele Ressourcen (CPU, RAM) ein Docker Container benötigt.

---

### Verwendeter Befehl

Zur Analyse der Container-Performance wird folgender Befehl verwendet:

```bash
docker stats
```

Dieser Befehl zeigt in Echtzeit die Ressourcennutzung aller laufenden Container an.

---

### Beispiel-Output

```stats
CONTAINER ID   NAME              CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O     PIDS
c3f3c024507b   pythonwebserver   0.03%     14.06MiB / 7.577GiB   0.18%     13.1kB / 4.78kB   0B / 1.72MB   1
```

---

### Geforderte Informationen

Aus dem Output werden die relevanten Werte entnommen:

```stats
CONTAINER ID   c3f3c024507b
NAME           pythonwebserver
CPU %          0.03%
MEM USAGE      14.06MiB / 7.577GiB (0.18%)
NET I/O        13.1kB / 4.78kB
BLOCK I/O      0B / 1.72MB
PIDS           1
```

---

### Cheatsheet

#### Performance anzeigen

```bash
docker stats
```

Zeigt:

- CPU-Auslastung
- RAM-Verbrauch
- Netzwerkaktivität
- laufende Container

#### Nur bestimmte Container überwachen

```bash
docker stats pythonwebserver
```

#### Einmalige Ausgabe (kein Live-Update)

```bash
docker stats --no-stream
```

---

### Erklärung der Werte

- `CPU %` – Wie stark der Container die CPU nutzt
- `MEM USAGE` – Wie viel Arbeitsspeicher aktuell verwendet wird
- `LIMIT` – Maximal verfügbarer RAM

---

### Fazit

Mit `docker stats` kann schnell und einfach überprüft werden, wie effizient ein Container läuft und ob er zu viele Ressourcen verbraucht.

## Portainer mit Docker Compose

### Ziel der Aufgabe

Portainer soll nicht mehr manuell mit `docker run`, sondern über eine wiederverwendbare `docker-compose.yml` gestartet werden. Zusätzlich wird ein persistentes Volume verwendet.

---

### Vorgehen

#### 1. docker-compose.yml erstellen

Es wird eine Compose-Datei erstellt, welche den Portainer Container definiert:

```yaml
version: "3.8"
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer_with_volume
    restart: unless-stopped
    ports:
      - "8000:8000"
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /srv/portainer_data:/data
```

#### 2. Container starten

```bash
docker compose up -d
```

Der Container wird im Hintergrund gestartet.

#### 3. Zugriff auf Portainer

Portainer ist erreichbar unter:

```
http://localhost:9000
```

Beim ersten Start wird ein Benutzer erstellt und das Setup abgeschlossen.

#### 4. Container-Informationen speichern

```bash
docker inspect portainer_with_volume > docker-inspect.txt
```

#### 5. Volume überprüfen

Da ein Bind Mount verwendet wird, kann der Host-Pfad wie folgt überprüft werden:

```bash
ls -l /srv/portainer_data > volume-ls.txt
```

---

### Ablage im Repository

Im Repository wird ein neuer Ordner erstellt:

```
portainer/
├── docker-compose.yml
├── docker-inspect.txt
└── volume-ls.txt
```

---

### Erklärung: Persistentes Volume

Das Volume `/srv/portainer_data` wird verwendet, um Daten dauerhaft zu speichern.

Portainer speichert folgende Daten:

- Benutzerkonten
- Einstellungen
- Container-Konfigurationen

Ohne Volume würden diese Daten bei einem Container-Neustart verloren gehen.

---

### Verhalten ohne Volume

Wenn der Container und das Image gelöscht werden:

- Alle Daten gehen verloren
- Portainer startet wieder im Initialzustand
- Der Benutzer muss neu erstellt werden

---

### Fazit

- Portainer wurde erfolgreich mit Docker Compose betrieben
- Ein persistentes Volume wurde korrekt eingebunden
- Daten bleiben auch nach einem Neustart des Containers erhalten.

## Pi-hole Installation mit Docker und Portainer

### Ziel der Aufgabe

Pi-hole wurde als Docker Container über Portainer installiert, um DNS-Anfragen im Netzwerk zu filtern und Werbung sowie Tracking zu blockieren.

---

### Was ist Pi-hole?

Pi-hole ist ein DNS-basierter Werbe- und Trackingblocker.

Funktionsweise:

- Alle DNS-Anfragen im Netzwerk laufen über Pi-hole
- Werbe- und Trackingdomains werden gefiltert
- Erlaubte Seiten werden normal weitergeleitet

---

### Vorgehen (Installation über Portainer)

#### 1. Volume erstellen

In Portainer wurde ein Volume erstellt:

- Name: `pihole`
- Speicherort: `/var/lib/docker/volumes/pihole/_data`

Das Volume speichert Konfigurationen dauerhaft, auch nach einem Neustart oder dem Löschen des Containers.

#### 2. Container erstellen

Image:

```
pihole/pihole:latest
```

Container-Name:

```
pihole
```

#### 3. Ports konfigurieren

Folgende Ports wurden freigegeben:

- `53 TCP` – DNS
- `53 UDP` – DNS
- `80 TCP` – Web Interface
- `443 TCP` – HTTPS (optional)

DHCP Port 67 wurde nicht verwendet.

#### 4. Volumes mounten

```
/dnsmasq.d
/pihole
```

Beide Pfade wurden auf das erstellte Volume gebunden.

#### 5. Umgebungsvariablen setzen

- `TZ` = Europe/Berlin
- `WEBPASSWORD` = eigenes Passwort
- `DNS1` = Router-IP oder externes DNS
- `DNS2` = leer

#### 6. Container starten

Der Container wurde über "Deploy the container" in Portainer erfolgreich gestartet.

---

### Zugriff auf Pi-hole

Das Admin Interface ist erreichbar unter:

```
http://localhost/admin
```

oder:

```
http://<IP-des-Servers>/admin
```

---

### Test und Beweis der Funktion

#### DNS Test

```bash
nslookup google.com 127.0.0.1
```

Zeigt, dass Pi-hole als DNS antwortet.

#### Pi-hole Query Log

Im Webinterface sind folgende Informationen sichtbar:

- Alle DNS-Anfragen
- Blockierte Domains
- Clients im Netzwerk

#### Website Block Test

Eine Domain wurde in der Blacklist von Pi-hole eingetragen:

```
example.com
```

Ergebnis: Die Seite ist nicht mehr erreichbar.

---

### Warum ist ein Volume wichtig?

Das Volume sorgt dafür, dass:

- Einstellungen gespeichert bleiben
- Blacklists erhalten bleiben
- Benutzerkonfiguration nicht verloren geht

Ohne Volume wäre Pi-hole nach einem Neustart komplett zurückgesetzt.

---

### Fazit

- Pi-hole wurde erfolgreich über Portainer installiert
- DNS läuft über den Container
- Werbung kann zentral im Netzwerk blockiert werden
- Persistente Daten wurden korrekt über ein Volume gespeichert

---

### Hinweise

- DHCP Port 67 wurde bewusst nicht aktiviert
- DNS läuft über Pi-hole (`127.0.0.1` / Server-IP)
- Container läuft stabil über Docker