
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
