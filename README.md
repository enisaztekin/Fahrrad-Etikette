![Road_Rage_Logo](https://user-images.githubusercontent.com/88652185/193117489-801d656d-c976-4446-a82e-5b252b8c1128.png)
# Projektdokumentation *Road Rage war gestern*		

- [1 Einführung](#einfuehrung)
    - [1.1 Motivation](#motivation)
    - [1.2 Zielstellung](#zielstellung)
    - [1.3 Vorgehensweise](#vorgehensweise)
- [2 Setup](#setup)
    - [2.1 LED Matrix Setup](#matrixsetup)
        - [2.1.1 Hardware](#matrixhardware)
        - [2.1.2 Software](#matrixsoftware)
    - [2.2 Personenerkennung Setup](#personenerkennung)
        - [2.2.1 Hardware](#personenerkennunghardware)
        - [2.2.2 Software](#personenerkennungsoftware)
- [3 Erkennnung von Radfahrer:innen mit TinyML und Edge Impulse](#erkennung)
    - [3.1 Verbindung zwischen Edge Impulse und Arduino Board](#edgeimpulse)	
    - [3.2 Acquisition der Daten](#aquisition)
    - [3.3 Datenaufbereitung](#datenaufbereitung)	
    - [3.4 Annotieren der Daten](#annotieren)	
    - [3.5 Datenexploration](#datenexploration)
    - [3.6 Modellierung](#modellierung)
    - [3.7 Validierung](#validierung)	
    - [3.8 Deployment](#deployment)	
- [4 Ausgabe (LED-Matrix)](#ausgabe)
- [5 Learnings](#learnings)	
----

<a name="einfuehrung"></a>
## 1 Einführung

<a name="motivation"></a>
## 1.1 Motivation
Das Fahrrad als Transportmittel.
Aus diesem Grund sind die Radfahrenden, die möglichst sicher und schnell von A nach B gelangen wollen, eine wichtige Zielgruppe der [Radbahn](https://radbahn.berlin/de/ueber-radbahn). Dabei geht es nicht darum, die Radbahn als Rennstrecke anzusehen, sondern als Mobilitätsenabler. 
Neben der nötigen Bodenbeschaffenheit und Wegbreite ist das Verhalten der FahrradfarerInnen grundlegend für einen guten Verkehrsfluss. Dies hat einen direkten Einfluss auf die Sicherheit der Radbahn. Ein Szenario, das sich negativ auf die Geschwindigkeit und die Sicherheit auswirkt, sind mittig fahrende und sich nebeneinander fortbewegende Radfahrer:innen. Überholen ist dann entweder gar nicht möglich oder kann nur über die Gegenfahrbahn erfolgen. Das sorgt für Frustration und erhöht das Unfallpotenzial. Mit der Radbahn sollen Radfahrende miteinander an ihr Ziel kommen und aufeinander Rücksicht nehmen.
Leider ist die nögtige Fahrrad-Etikette nicht jedem bekannt und das soll auf der Radbahn mit technischer Unterstützung und den richtigen Hinweisen geändert werden.

<a name="zielstellung"></a>
## 1.2 Zielstellung
Mit Hilfe eines TinyML-Modells, das Bildaufnahmen von der Radbahn aufnimmt, sollen Radfahrende erkannt werden. Tritt dies ein, soll ein Signal (1 = Radfahrende zu sehen / 0 keine Radfahrenden zu erkennen) per Bluetooth an eine mit einem Mikrocontroller verbundene Leuchtmatrix gesendet werden und die sonst ausgeschaltete Matrix einschalten. Zu sehen ist dann eine Darstellung des gewünschten Verhaltens von Fahrradfahrenden auf der Radbahn. Das situationsbedingte Einschalten der LED Matrix sorgt für Aufmerksamkeit bei Fahrradfahrenden zudem muss die stromintensive LED Matrix nicht durchgehend angeschalten sein, was die Nachhaltigkeit der Anwendung erhöht. Eine hohe Genauigkeit bei der Objekterkennung ist zudem nicht nötig, da es sich nicht um für den Verkehr kritische Informationen handelt. 
Was auf den Rolltreppen in England funktioniert, ist auch auf deutschen Radwegen umsetzbar.

<a name="vorgehensweise"></a>
## 1.3 Vorgehensweise
Das Projekt kann in drei Pakete aufgeteilt werden. 
- Visualisierung auf einer LED Matrix (Output)
- Erkennung von Radfahrenden mit TinyML und Edge Impulse (Input)
- Verbindung von Input und Output mittels Bluetooth

Letzteres konnte aufgrund der zeitlichen Begrenzung nicht umgesetzt werden. Verweis Frank xxx

Nach dem Zusammenstecken der Hardware konnte die Softwareseite betrachtet werden. Für das Erstellen eines Modells zur Klassifikation der Bilder (Input) ist Edge Impulse im Einsatz. Dabei handelt es sich um eine Plattform für maschinelles Lernen auf Edge Geräten, wozu Kameras und weitere Sensoren zählen. Mit Hilfe von Edge Impulse kann das mit eigenen Bilddaten von der Radbahn angelernte Modell so komprimiert werden, dass es auf den mit der Kamera verbundenen Mikro-Controller geladen werden kann. Dieses Konzept wird [TinyML](https://www.tinyml.org/) genannt und umfasst spezielle Hard- und Software sowie Algorithmen. 

Die Einschränkungen in der Hardware ermöglichen es nur eine Klassifikation der Bilddaten vorzunehmen und dabei zu bestimmen, ob Fahrradfahrende zu sehen sind oder nicht. Ohne diese Beschränkungen könnten auch leistungsstärkere Algorithmen eingesetzt werden, die beispielsweise erkennen können, ob Fahrradfahrende zu sehen sind, wie viele und an welchen Stellen sie sich im Bild befinden. Siehe [Edge Impulse](https://docs.edgeimpulse.com/docs/edge-impulse-studio/learning-blocks/object-detection/fomo-object-detection-for-constrained-devices).

So könnte ein zukünftiger Prototyp so konzipiert werden, dass die Matrix lediglich dann aufleuchtet, wenn sich Radfahrende nebeneinander fortbewegen.

Edge Impulse (öffentliches Projekt): https://studio.edgeimpulse.com/public/138316/latest


<a name="setup"></a>
## 2 Setup

<a name="matrixsetup"></a>
## 2.1 LED Matrix Setup
In den folgenden Abschnitten werden die verwendete Hardware und Software von der LED Matrix näher beschrieben.
<a name="matrixhardware"></a>
## 2.1.1 Hardware
Für die LED-Anzeige werden die folgenden Hardwarekomponenten benötigt.

| Komponente                                  | Beschreibung                                                                                                                     | Bild |
|---------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------|------|
| ESP-WROOM-32                                | Microcontroller auf dem der Code für die LED-Anzeige gespeichert ist und der sich mit dem WLAN und mit Bluetooth verbinden kann  |   ![esp32](https://user-images.githubusercontent.com/44236199/192882507-bdc18236-7096-4b0a-aea1-f33fe07cc0f6.jpg)   |
| 64x32 RGB LED MATRIX – 5MM Pitch            | LED Matrix auf der die gewünschten Anzeige abgebildet werden                                                                     |   ![WaveShare-RGB-Full-Color-LED-Matrix-Panel-64x32-Pixels](https://user-images.githubusercontent.com/44236199/192883322-65fe3dd6-b2ea-45de-b7e4-334a9e1ae5ce.jpg)   |
| 5V 3 Ampere Netzteil                        | Netzteil für die Stromzufuhr an die LED Matrix und an den ESP32                                                                  |  ![netzteil](https://user-images.githubusercontent.com/44236199/192882575-5a93a7e4-64e7-4907-8bfd-5816602403a8.jpg) |
| Micro USB Kabel                             | Mit dem Micro USB Kabel wird der Code vom Rechner auf den ESP32 hochgeladen                                                      |   ![mini-usb](https://user-images.githubusercontent.com/44236199/192882605-9bd6f867-327b-4676-80cf-ab1b6ac60ec3.jpg) |
| Micro USB Kabel mit zwei offenen Kabelenden | Das Micro USB Kabel mit zwei offenen Kabelenden wird für die Versorgungsspannung ans ESP32, ohne ein zweites Netzteil, benötigt  |    ![31MO4TzwE6L _SY445_SX342_QL70_ML2_](https://user-images.githubusercontent.com/44236199/192882622-953ea12e-6301-4b15-aa46-fec2b3d049ab.jpg)  |
| mindestens 15 Female to Female Jumper Kabel | Mit den Jumper Kabel werden die Pins vom ESP32 mit den Pins an der LED Matrix verknüpft                                          |   ![female to female](https://user-images.githubusercontent.com/44236199/192882636-46051648-e847-4914-ba35-ee244fdf4340.jpg)  |
| Terminal Block                              | Der Terminal Block koppelt die Stromzufuhr vom ESP32 und der LED Matrix                                                          |   ![terminalblock](https://user-images.githubusercontent.com/44236199/192882655-c67db23f-0639-4d7b-bf78-95bc2fc72444.png) |

### Verknüpfung vom ESP32 mit der LED-Matrix
<img width="500" alt="pinout" src="https://user-images.githubusercontent.com/44236199/192893873-b1f94d4c-88a3-4cc1-a72e-53bdac19f991.PNG">

Das sollte dann so ähnlich wie im folgenden Bild aussehen

<img width="650" alt="pinout2" src="https://user-images.githubusercontent.com/44236199/192895515-9944837b-595a-47da-b175-daa99a81432d.jpeg">

Hinweis: Falls es die fertige Anzeige flackert, kann man auch den zweiten GND von der LED Matrix frei lassen und dann überprüfen, ob das Flackern aufhört.
<a name="matrixsoftware"></a>
## 2.1.2 Software
Als Entwicklungsumgebung wurde, für die LED Matrix, [Arduino IDE](https://www.umwelt-campus.de/fileadmin/Umwelt-Campus/IoT-Werkstatt/octopus/Quickstart.pdf) verwendet. Danach muss als erstes das ESP32 Board in die Ardiuno IDE instaliert werden. Eine entsprechende Anleitung gibt es [hier](https://randomnerdtutorials.com/installing-the-esp32-board-in-arduino-ide-windows-instructions/). Anschließend gilt es die Bibliotheken [Adafruit GFX](https://github.com/adafruit/Adafruit-GFX-Library) , [ESP32-HUB75-MatrixPanel-I2S-DMA](https://github.com/mrfaptastic/ESP32-HUB75-MatrixPanel-I2S-DMA) und [Animated GIF](https://github.com/bitbank2/AnimatedGIF) in die IDE hinzuzufügen.

Der verwendete Code stammt ebenfalls von der Bibliothek ESP32-HUB75-MatrixPanel-I2S-DMA unter dem Namen [AnimatedGIFPanel.ino](https://github.com/mrfaptastic/ESP32-HUB75-MatrixPanel-I2S-DMA/tree/master/examples/AnimatedGIFPanel), da das uns ermöglicht beliebige Inhalte als GIFs auf der LED Matrix anzuzeigen, ohne jedes Mal den Code anpassen zu müssen. Dafür muss vorab noch das Plugin [Arduino ESP32 filesystem uploader](https://github.com/me-no-dev/arduino-esp32fs-plugin) installiert werden.

Wenn die oberen Schritte erfolgreich abgeschlossen wurden, können die gewünschten GIFs in den /data/gifs hinzugefügt werden und der Code auf das ESP32 hochgeladen werden.

Hinweis: Falls beim Kompilieren und Hochladen des Codes aud den ESP32 keine Fehler angegeben wurden, aber die Anzeige trotzdem nicht richtig funktioniert (z.B. werden nicht alle Farben  und Formen angezeigt), dann ist es ratsam onchmal die Hardware zu überprüfen. Vorallem jegliche Kabel neigen dazu schnell beschädigt zu werden ohne das man etwas bemerkt.


<a name="personenerkennung"></a>
## 2.2 Personenerkennung

<a name="personenerkennunghardware"></a>
## 2.2.1 Hardware

Für die Erkennung von Fahrradfahrer:innen wird das Tiny Machine Learning “Harvard Kit” benötigt. Das beinhaltet folgende Bausteine:
- Arduino Nano 33 BLE Sense Lite Board
- Arduino Tiny Machine Learning Shield
- ArduCam OV7675
- USB A to USB Micro B Kabel (1 Meter)

Relevante technische Daten des Arduino Nano 33 BLE Sense Lite Board: (Arduino 2022)
- Microcontroller: nRF52840
- Betriebsspannung: 3.3Volt
- Eingangsspannung: 21Volt
- CPU Flash Memory: 1MB 
- SRAM: 256KB
- Eingebettete Sensoren:
    - Mikrofon
    - Barometrischer Sensor
    - Feuchtigkeits-, Druck- und Temperatursensor
    - Gesten-, Näherungs- Lichtfarben- und Lichtintensitätsensor

In der folgenden Abbildung sind das Arduino 33 BLE Sense Lite Board und die ArduCam OV7675 auf dem Arduino Tiny Machine Learning Shield zusammengesteckt.

![Bildschirmfoto 2022-09-21 um 23 09 20](https://user-images.githubusercontent.com/72546527/192510460-8d22e887-9058-4fe0-ae50-d6fbf1f59e84.png)

**Vorbereitung und Testen der benötigten Hardware:**

Bevor die Hardware mit der Softwareplattform Edge Impuls verbunden wird, muss geprüft werden, ob die angewendeten Elemente funktionieren.

Für die Vorbereitung und das Testen der Hardware wurde folgendes Endgerät genutzt:
- MacBook Pro (M1, 2020)
- Betriebssystem: macOS Monterey

Für macOS müssen die drei Schritte durchgeführt werden:
1. Schritt: USB-Treiber
- Der USB-Treiber von SiLabs herunterladen und die dmg-Datei installieren: https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers 
2. Schritt: Arduino-IDE
- https://www.arduino.cc/en/Main/Software 
- Hierfür wird die Arduino IDE verwendet. Die aktuellste Version ist die Arduino IDE 2.0.0:

<img width="907" alt="Bildschirmfoto 2022-09-20 um 03 33 51" src="https://user-images.githubusercontent.com/72546527/192511108-1a35b276-12d3-43fd-83af-44afa6645781.png">

3. Schritt: Arduino Nano 33 BLE Board auswählen + Libraries installieren
- Board “Arduino Mbed OS Nano Boards” installieren:

<img width="317" alt="Bildschirmfoto 2022-09-22 um 01 12 12" src="https://user-images.githubusercontent.com/72546527/192511849-21284d64-272e-438b-895d-c31061d06772.png">

- Drei Libraries installieren:

<img width="319" alt="Bildschirmfoto 2022-09-22 um 01 14 58" src="https://user-images.githubusercontent.com/72546527/192511925-39b7e51f-94a8-487b-9820-b7a47ed8a9ab.png">
<img width="310" alt="Bildschirmfoto 2022-09-22 um 01 15 24" src="https://user-images.githubusercontent.com/72546527/192511981-fa2f8135-dd90-4603-a082-a55c1fb8f1e6.png">
<img width="314" alt="Bildschirmfoto 2022-09-22 um 01 15 49" src="https://user-images.githubusercontent.com/72546527/192512025-1d10ea58-960b-4066-9e53-1112b09e0101.png">

4. Schritt: Beispielcode 
- Es gibt bereits einen Beispielcode, um die Kamera zu testen.
- File > Examples > Harvard_TinyMLx > test_camera

![Bildschirmfoto 2022-09-20 um 04 09 49](https://user-images.githubusercontent.com/72546527/192512198-d212c3e0-3be6-4383-8046-6837041c1284.png)

- Das Board “Arduino Nano 22 BLE” auswählen:

<img width="529" alt="Bildschirmfoto 2022-09-24 um 16 13 34" src="https://user-images.githubusercontent.com/72546527/192512337-81dbea70-f4cc-4c6e-a74c-283613bd0723.png">

- Verify
- Upload:
<img width="525" alt="Bildschirmfoto 2022-09-22 um 01 26 09" src="https://user-images.githubusercontent.com/72546527/192512679-c00c5824-d582-48bd-9f38-ffb2a96f0da6.png">

- Serial Monitor öffnen. 
- “Both NL & CR” und 115200 baud” auswählen.
- Befehle:
    - ```single``` + CMD + ENTER (Einzelbild wird von der Kamera erstellt und für jedes Pixel wird eine Hexadezimalzahl ausgegeben.)
    - ```live``` + CMD + ENTER (Die Rohbytes der Bilder werden live über die serielle Verbindung.)
    - ```capture``` + CMD + ENTER (Löst im Einzelmodus eine Bildaufnahme aus.) 
- Ausgabe bei den Befehlen single und dann capture:

<img width="661" alt="Bildschirmfoto 2022-09-22 um 01 28 15" src="https://user-images.githubusercontent.com/72546527/192513252-d4ae8c22-d31b-420d-b843-444dc9c3bebd.png">

- Das Ergebnis zeigt, dass die Kamera funktioniert. Jedoch können im Serial Monitor keine Zeilen kopiert werden. Der Aufwand wäre daher zu hoch, um die Hexadezimalzahlen zu entnehmen und abbilden zu lassen.

<a name="personenerkennungsoftware"></a>
## 2.2.2 Software
Für das Projekt wird Edge Impulse, welche eine Softwareplattform für die Entwicklung für das maschinelles Lernen (eng. Machine Learning (ML)) ist, genutzt. Es ermöglicht Entwickler:innen Lösungen mit realen Daten zu erstellen und zu optimieren. Durch den Prozess der Erstellung, Bereitstellung und Skalierung von eingebetteten ML-Anwendungen wird das Arbeiten einfacher, schneller und ermöglicht, Geräte intelligenter zu machen. (Edge Impuls 2022)

**Setup Edge Impuls für MacOS:**
1. Schritt: Einen Account und Projekt erstellen bei Edge Impuls:
- https://studio.edgeimpulse.com/login 

2. Schritt: Links auf der Webseite auf “Devices” klicken:

<img width="228" alt="Bildschirmfoto 2022-09-28 um 09 43 07" src="https://user-images.githubusercontent.com/72546527/192719179-c024575f-9900-4f4f-8929-5721d3e7d147.png">

3. Schritt: Um ein neues Gerät zu verbinden, auf “Connect a new device”, dann auf “Browse dev boards” und dann auf Arduino Nano 33 BLE Sense klicken:

<img width="537" alt="Bildschirmfoto 2022-09-24 um 16 32 00" src="https://user-images.githubusercontent.com/72546527/192718980-09f60ce9-b524-4016-a027-7e64bf786738.png">

4. Schritt: Installierung der Dependencies auf macOS:
- Edge Impuls Dokumentation für die Installierung der notwendigen Dependencies für das Arduino Nano 33 BLE Board: https://docs.edgeimpulse.com/docs/development-platforms/officially-supported-mcu-targets/arduino-nano-33-ble-sense 
    1. Edge Impulse CLI:
    - Installierung Python 3 auf dem Computer: https://www.python.org 
    - Installierung Node.js v14 oder neuer: https://nodejs.org/en/ 
    - Installierung des Edge Impulse CLI Tools im Terminal: ```npm install -g edge-impulse-cli --force```
    - Es sollten nur “WARN” und folgende Informationen angezeigt werden:

        <img width="894" alt="Bildschirmfoto 2022-09-24 um 16 40 07" src="https://user-images.githubusercontent.com/72546527/192719303-267a62a6-8348-4b91-b9bf-5d0b7d376d4f.png">
        
	- Trouble Shooting:
        - Fehlermeldung code 1: Die Python Dependencies sind nicht richtig konfiguriert:
        
        <img width="941" alt="Bildschirmfoto 2022-09-24 um 16 48 47" src="https://user-images.githubusercontent.com/72546527/192719414-0a3bbdcd-bd90-4d15-afbb-644d9a77749d.png">

        - 1. Deinstallierung des CLI Tools: ```sudo npm uninstall npm -g```
        - 2. Command Line Tool installieren: ```xcode-select --install```
        - 3. Node über homebrew installieren: ```brew install node```
        - 4. Python3 über homebrew installieren: ```brew install python3```
        - 5. Version von Python 3.10. installieren: ```pyenv install 3.10.```
        - 6. Edge Impuls CLI Tool erneut installieren: ```npm install -g edge-impulse-cli --force```

    2. Arduino CLI:
        - Dokumentation für macOS: https://arduino.github.io/arduino-cli/0.27/installation/
        - Im Terminal: ```brew update``` ```brew install arduino-cli```

    3. Verbindung zu Edge Impuls:
        - USB Kabel an Arduino Nano 33 BLE Sense und dem Computer verbinden
        - 2x auf dem Reset-Button (weiß) klicken → Das gelbe/orange Licht pulsiert
        - Edge Impulse Firmware herunterladen: https://cdn.edgeimpulse.com/firmware/arduino-nano-33-ble-sense.zip
        - Zip-Datei öffnen, die Dateien “arduino-nano-33-ble-sense.ino.bln” und “flash_mac.command” in  den Benutzerordner verschieben
        - “flash_mac.command” öffnen:
        
        <img width="449" alt="Bildschirmfoto 2022-09-24 um 17 32 44" src="https://user-images.githubusercontent.com/72546527/192719602-3581bd55-c6cd-490b-b7da-2eab330d5b7f.png">

        - Wenn das Blinken des Lichtes auf dem Arduino Board aufhört, auf den Reset-Button (weiß) klicken, um die neue Firmware zu launchen:
        
        <img width="895" alt="Bildschirmfoto 2022-09-24 um 17 57 15" src="https://user-images.githubusercontent.com/72546527/192720337-263dedd7-6515-453c-828a-a6a089dff3a7.png">

        - Das Arduino-Board ist weiterhin angeschlossen mit dem Computer und ein neues Terminal-Fenster öffnen und folgenden Befehl eingeben: ```edge-impulse-daemon```
        - Auf der Edge Impulse Device Seite erscheint das Gerät:
        <img width="951" alt="Bildschirmfoto 2022-09-20 um 14 48 26" src="https://user-images.githubusercontent.com/72546527/192720521-0dd6c372-2928-4695-8681-41156592f847.png">

<a name="erkennung"></a>
## 3 Erkennnung von Radfahrer:innen mit TinyML und Edge Impulse
Nach der kostenfreien Anmeldung auf Edge Impulse kann ein neues Projekt angelegt werden. Auf der linken Seite befinden sich dann die zu durchlaufenden Schritte von der Verbindung mit dem Edge Gerät über die Datenacquistion und das Modellieren bis zum Deployment der finalen Modellversion. Bereits durchgeführte Schritte werden dabei grün angezeigt. Siehe [Edge Impulse](https://www.edgeimpulse.com/)

<img width="111" alt="EdgeImpulse_Steps" src="https://user-images.githubusercontent.com/64984929/193010449-c5d62f2e-4f4a-4652-b7a3-14120211b50e.png">
Durchzuführende Schritte in Edge Impulse (Anleitung)


Im Rahmen eines vergleichbaren Projektes ist es empfehlenswert, eine Reihe an Iterationen bei unzureichender Modellgenauigkeit im Testing zu durchlaufen. Die Genauigkeit kann durch Erhöhung der Datengrundlage, der Überarbeitung der Annotierung ebendieser, der Auswahl des eingesetzten Modells und der Hyperparameteranpassung erfolgen.

<a name="edgeimpulse"></a>
## 3.1 Verbindung zwischen Edge Impulse und Arduino Board

Um die Verbindung zu prüfen, muss zuallererst ein Login auf der Edge Impulse Webseite erfolgen und das gewünschte Projekt ausgewählt werden. Der Name des Projektes ist hier “R(o)AD RAGE war gestern. 2.0”:

<img width="713" alt="Bildschirmfoto 2022-09-24 um 16 20 14" src="https://user-images.githubusercontent.com/72546527/192827009-166b10ec-6bcf-4191-99fd-251cdb051d70.png">

Bei “Devices” sollte das gewünschte Board bereits angezeigt werden, jedoch ist bei “Remote Management” das Licht rot. Dann das Arduino Nano 33 BLE Sense Board mit dem USB Kabel an den Computer verbinden. Bei einem macOS Gerät im Terminal folgenden Befehl eingeben: ```edge-impulse-daemon```
 
Das Licht vom Arduino Board bei “Devices” auf Edge Impulse sollte nun grün sein.
Ein Gerät kann nur an einem Edge Impulse Gerät verknüpft werden. Bei einem Wechsel muss der Terminal-Befehl auf dem macOS Gerät eingeben: ```edge-impulse-daemon --clean```

Die gleichen Logindaten wie bei Edge Impuls eingeben und aus den bereits vorhanden Projekten, das Richtige auswählen:

![Bildschirmfoto 2022-09-24 um 18 44 39](https://user-images.githubusercontent.com/72546527/192827151-482a5ee8-a470-476c-aff3-fd11c784d2eb.png)

Das Arduino Board muss bei den folgenden Schritten nicht angeschlossen werden, da vorerst Daten gesammelt, aufbereitet und das Modell erstellt werden. Erst beim Deployment ist der Anschluss notwendig.

<a name="aquisition"></a>
## 3.2 Acquisition der Daten

Zum Training eines Modells zur Klassifizierung von Bildern direkt beim Erheben der Daten von der Kamera (Edge) müssen Rohdaten von den möglichen Situationen auf der Radbahn gesammelt werden. Zwei mögliche Herangehensweisen können dabei in Betracht gezogen werden und werden im Folgenden mit den jeweiligen Vor- und Nachteilen.

#1 Mit der Sensorik Bilder direkt in Edge Impulse laden
Vorteile: Test der Technik, Nachvollziehbarkeit Verarbeitung
Nachteile: Geringe Bildqualität und Latenz, Bilder einzeln labeln, schwierige Handhabung „im Feld“
   
<img src="https://user-images.githubusercontent.com/64984929/193002169-4daba362-b3bf-4542-b6ac-52db4ac85859.jpg" width=50% height=50%>
Schwierige Handhabung auf der Radbahn

<img src="https://user-images.githubusercontent.com/64984929/193002546-0541f262-f645-4310-8108-a5145645bd32.jpg" width=50% height=50%>
Geringe Bildqualität bei der Aufnahme mit Arduino Nano Sense

#2 Massenupload von Handyaufnahmen in Edge Impulse
Vorteile: effiziente Videoaufnahmen möglich, gute Qualität für das Labeln in Ordnern, einfache Handhabung
Nachteile: Tool zur Datenaufbereitung nötig

<img src="https://user-images.githubusercontent.com/64984929/193004059-0701755d-306a-4cdb-a56c-9a102894f0ca.png" width=50% height=50%>
Einfache Handhabung auf der Radbahn

Aufgrund der einfachen Handhabung ist die zweite Methodik (Videoaufnahmen mit Handy) eingesetzt worden. Dabei wurde darauf geachtet, dass die Kamera aus einem erhöhten Punkt die Bilder aufnimmt, an dem in der Zukunft auch die Kamera positioniert werden kann.

Um ausreichend Trainingsdaten möglichst unterschiedlicher Szenarien zu generieren und somit dem Klassifikationsmodell mehr Beispieldaten zum Lernen zur Verfügung zu stellen, sind acht Videos aufgenommen worden. 
Von einzelnen Radfahrenden, über Gruppen, die sowohl in die gleiche als auch entgegengesetzte Richtung fahren, wurden auch sich hintereinander sowie nebeneinander fortbewegende Radfahrende aufgenommen. Dabei wurden unterschiedliche Kleidungsstücke in einer Reihe an Farben eingesetzt und Accessoires, wie ein Regenschirm, verwendet.

<img src="https://user-images.githubusercontent.com/64984929/193005041-0689aaa9-c829-4d02-9e35-765e0f825301.png" width=50% height=50%>
<img src="https://user-images.githubusercontent.com/64984929/193005070-38cb68b4-baf0-4ef5-8084-457303378e7e.png" width=50% height=50%>
<img src="https://user-images.githubusercontent.com/64984929/193005146-b657845b-f500-4662-a810-5cd07de8aa7e.png" width=50% height=50%>

Neben den Bildern, auf denen Radfahrende in der richtigen Entfernung zu sehen sind, müssen im Trainingssatz auch Bilder ohne Radfahrende enthalten sein, sodass der Algorithmus auch auf diese Situationen trainiert wird. 

<img src="https://user-images.githubusercontent.com/64984929/193013004-ca31e40e-5a65-41bd-b5b9-ae34aff34cec.png" width=50% height=50%> <br>
Leere Radbahn

Das angeschlossene Fahrrad an der Seite hilft dem Algorithmus zusätlich dabei, dass nur Fahrräder in Verbindung mit einem Radfahrenden einen Auslöser für die Matrix darstellen, da das obige Bild als 0 (keine Radfahrende) klassifiziert wurde. 

<a name="datenaufbereitung"></a>
## 3.3 Datenaufbereitung
Aus den aufgenommenen Videodateien müssen einzelne Frames extrahiert werden, sodass sie auf Edge Impulse hochgeladen werden können.
Da der später eingesetzten Algorithmen (MobileNetV1 oder MobileNetV2) eine höhere Genauigkeit basierend auf quadratischen Bildquellen erzielt, wurden die mit einem iPhone aufgenommen Videoaufnahmen direkt auf dem Endgerät in ein quadratisches Format umgeformt. Dieser Schritt ist nicht zwingend notwendig, da Edge Impulse diesen Schritt im späteren Verlauf anbietet. Allerdings kann so die Methode der Größenanpassung eher beeinflusst werden.
Für die Extraktion der Frames aus den Videos wurde der kostenfreie VLC Player heruntergeladen, der eine Funktion anbietet, welche in vorher eingestellten Abständen Frames aus einem Video in einem definierten Ordner ablegt. Die genaue Beschreibung dazu ist in dem Video dargestellt.  (2 Easy Ways to Extract Frames from a Video [with High Quality] - Bing video)

Achtung: Die extrahierten Frames müssen gegebenenfalls gedreht werden.

<a name="annotieren"></a>
## 3.4 Annotieren der Daten
Da es sich bei der Klassifizierung von Bildern um einen Algorithmus des überwachten (supervised) maschinellen Lernens handelt, wird das Modell auf gekennzeichneten (labeled) Daten trainiert. Die Rohdaten in Form von Bildern müssen aus diesem Grund manuell mit einer Beschriftung versehen werden. Dieser Schritt wird Annotierung genannt.
Annotationslogik:
0 - keine Radfahrende auf dem Bild zu erkennen
1 - Radfahrende auf dem Bild zu erkennen

Die Rohdaten können gesammelt in Edge Impulse hochgeladen werden und durch das Öffnen jedes einzelnen betrachtet und entsprechend annotiert werden. Das ist aufwändig und, wie in diesem Anwendungsfall mit über 300 Bilddateien, nicht effizient.
Alternativ dazu können die Bilder auch lokal entsprechend der Annotationslogik in dafür angelegte Ordner verschoben werden. Diese können dann nacheinander gemäß der Annotation hochgeladen werden. Zudem kann wie in dem folgenden Bild ein Split der Daten in Trainings- und Testdatensatz durchgeführt werden.53

![Upload](https://user-images.githubusercontent.com/64984929/193027396-b06fd017-75fd-4104-a935-e2ee6f06ed90.png)
Edge Impulse Data Acquisition

Ein Autolabelling ist nicht empfehlenswert, da der Anwendungsfall mit der Radfahrendenerkennung sehr spezialisiert ist und es dafür noch keine vorgefertigten Algorithmen gibt.

Nach mehreren Iterationen des Annotierens und Entfernens von überflüssigen Bildern (sehr ähnliche Bilder, fast Duplikate) bilden 312 Aufnahmen die Datengrundlage mit folgender Verteilung:

![split](https://user-images.githubusercontent.com/64984929/193031394-71312c21-5893-4541-95d2-6d860b8e8aef.png)

Von den 243 Elementen im Trainingsdatensatz sind 112 als 1 annotiert und 131 als 0 annotiert.

Dementsprechend sind die beiden Gruppen (0 und 1) fast gleich groß.

Die hohe Zahl an als 0 klassifizierten Bildern ist damit zu erklären, dass Radfahrende am hinteren Ende des Radweges in der ersten Iteration vom Algorithmus schwer zu erkennen waren und die Genauigkeit niedrig war. Ab welcher Entfernung Radfahrende als solche erkannt werden sollen wurde im Rahmen des Prototypen willkürlich festgelegt (Höhe des angeschlossenen Fahrrades an der Seite). Um den Übergang deutlicher zu kennzeichnen wurden deshalb eine Reihe an Bildern mit Radfahrenden kurz hinter dem angeschlossenen Fahrrad entfernt. 
Zukünftig sollte die Kamera so eingestellt sein, dass die Entfernung geringer ist, was durch die Neigung der Kamera erreicht werden könnte. Dabei sollte allerdings beachtet werden, dass dadurch die abgedeckte Strecke sich verringert und womöglich sich schnell fortbewegende Radfahrende mit der aktuellen Bilderfassungsfrequenz nicht erfasst werden.


<a name="datenexploration"></a>
## 3.5 Datenexploration
Um ein Gefühl für die Daten und für die technische Verarbeitung der Bilder zu bekommen, bietet Edge Impulse einen Data Explorer an.
Dieses visuelle Werkzeug unterstützt dabei, Ausreißer und falsch oder gar nicht annotierte Bilder zu erkennen. Dazu wird ein Algorithmus eingesetzt, der aussagekräftige Eigenschaften aus den Bildern extrahiert (Kanten, Kontraste, Muster). Diese werden dann mit einem weiteren Algorithmus zur Dimensionalitätreduktion in die unten sichtbare zweidimensionale Darstellung umgewandelt. Data explorer - Edge Impulse Documentation

<img width="768" alt="Data Explorer" src="https://user-images.githubusercontent.com/64984929/193038931-e51d0344-2595-4e1b-8f12-ed4c1ecc3275.png">
Data Explorer mit Möglichkeit zum Vergeben einer neuen Annotatierung mit Bild


<a name="modellierung"></a>
## 3.6 Modellierung
In Edge Impulse wird der Modellierungsprozess "Impulse Design" genannt. Im ersten Schritt wurden dabei die empfohlenen Standards für die Feature-Generierung aus den Trainingsdaten gewählt. Auch für das Transfer Learning, was einen bereits vortrainierten Algorithmus zur Klassifizierung von Bildern mit den zuvor generierten Features aus den Trainingsdaten anpasst, wurde die empfohle Standardeinstellung ausgewählt. Alternativ können auch individuelle Algorithmen eingesetzt werden, die vorher in die Plattform geladen werden müssen. Der Output stellt das Ergebnis des Modells dar, dass entsprechend der Annotationslogik entweder 0 oder 1 ist.


<img width="934" alt="image" src="https://user-images.githubusercontent.com/64984929/193039542-97c6a9dd-1d42-45a7-a672-32ccb9fbe2f6.png">
Create Impulse in Edge Impulse

Die für das Modelltraining generierten Features im Unterpunkt Image erzeugen folgende Darstellung wenn sie wieder auf die zweidimensionale Ebene projeziert werden.

<img width="369" alt="image features 2 0" src="https://user-images.githubusercontent.com/64984929/193043038-f77945eb-482b-4149-88ac-3a6987db984a.png">
Features mit Möglichkeit zum Annotieren und Bild


Im Bereich Transfer Learning können im oberen Bereich die Trainings Settings angepasst werden. Dabei wurden die Standardeinstellungen übernommen mit Ausnahme von Data Augmentation. Dabei werden die Bilder zufällig während des Trainings transformiert und somit die Diversität der Daten erhöht. Aufgrund der geringen Menge an Daten ist dies sinnvoll. Siehe [TensorFlow](https://www.tensorflow.org/tutorials/images/data_augmentation)
Im unteren Bereich ist die Wahl unterschiedlicher Algorithmen möglich.

<img width="532" alt="Transfer learning" src="https://user-images.githubusercontent.com/64984929/193043988-f9ddc43d-ed80-4f43-ac62-5f9efc1d3d14.png">


Bei der Wahl eines passenden Algorithmus muss auf die Leistung der Hardware geachtet werden. Lediglich die mit MobileNetV1-Modelle können auf dem Arduino Nano Sense 33 BLE laufen, da dieser nur über einen Arbeitsspeicher von 256 KB verfügt. Siehe [ArduionoFactSheet](https://docs.arduino.cc/hardware/nano-33-ble-sense)
Im Test konnte die höchst mögliche Genauigkeit das Modell erzielen: MobileNetV1 96x96 0.25 (no final dense layer, 0.1 dropout)

<img width="400" alt="Model" src="https://user-images.githubusercontent.com/64984929/193047212-5ad30c5e-2a4b-45ec-b0f1-255b8c6b8e3a.png">
Auflistung der auswählbaren Modelle


<a name="validierung"></a>
## 3.7 Validierung
Nach dem Training eines neuen Modells wird auch eine Validierung durchgeführt, um die Leistung des Modells zu erkennen. Entsprechen die Ergebnisse nicht den Anforderungen des Anwendungsfalls gelten sie als Ausgangspunkt für die nächste Iteration. 

Neben der Genauigkeit (Accuracy) des Modells wird eine Konfusionsmatrix, welche die Ergebnisse des Klassifikationsmodells auf die Validierungsdaten darstellt, ausgegeben. 
Im bestmöglichen Modell (2.4) der fünf erstellten, wurde eine gute Genauigkeit erreicht. Auffallend ist, das 40% der Bilder ohne Radfahrende im Validierungsdatensatz als Bilder mit Radfahrenden klassifiziert wurden. Dies ist allerdings nicht zu kritisch zu bewerten, da es sich dabei oft um Bilder handelt, in denen die Radfahrenden kurz vor dem Erkennungsbereich sind und sowieso im nächsten Frame erkannt werden würden. Zusätzlich ist es für betrachteten Anwendungsfall nicht bedenklich, wenn die LED Matrix etwas öfters aufleuchtet als sie eigentlich müsste. Eine nahezu perfekte Genauigkeit auf Edge Devices zu erwarten, ist nicht realistisch.

<img width="389" alt="validation1" src="https://user-images.githubusercontent.com/64984929/193067675-1b2f7586-3449-4e85-a1c8-7d2be7508be7.png">
Leistungsübersicht des Modells


Als sehr benutzerfreundlich ist in der Übersicht ein weiterer Data Explorer zu bewerten, der die unterschiedlihen Klassifizierungen farblich hervorhebt, das Filtern dieser zulässt und die einzelnen Bilder nach Auswahl anzeigt. So können die fehlklassifizierten Bilder identifiziert und darauf aufbauend Anpassungen am Datensatz oder der Annotierung durchgeführt werden.

<img width="383" alt="validation2" src="https://user-images.githubusercontent.com/64984929/193067766-859386d3-6d85-421f-8d16-a9541370e151.png">
Data Explorer



Zudem kann im unteren Teil der Übersicht die zukünftige Leistung des Modells beim Durchlaufen auf dem Edge Device abgelesen werden. Mit maximal 130,9KB benötigtem Arbeitsspeicher läuft dieses Modell entsprechend auch auf dem Arduino Nano Sense 33 BLE.

Im Rahmen der Modelloptimierung wurden auch die Einstellungen im Bereich der Training Settings angepasst. Die Anzahl an zu durchlaufender Trainingszyklen wurde erhöht und die Lernrate minimiert, was für einen detaillierteren Lernprozess sorgt. Allerdings hat dies zur Überanpassung des Modells auf die Trainingsdaten geführt und angewendet auf die Validierungsdaten nur eine Genauigkeit von 42,9% erreicht.

<img width="809" alt="vali3" src="https://user-images.githubusercontent.com/64984929/193069638-e1d7a92a-cc81-443b-a136-268e0173b797.png">
Überanpassung des Modells auf Trainingsdaten


Mit einer leistungsstärkeren Hardware könnten auch Algorithmen angewendet werden, die mehr Arbeitsspeicher benötigen. Im Vergleich zu den vorherigen Modellen könnte so ein deutlich genaueres Ergebnis erzielt werden, wie das mit dem unten verwendeten MobileNetV2-Modell zu sehen ist.


<img width="849" alt="vali4" src="https://user-images.githubusercontent.com/64984929/193070927-7767d9b9-7156-4cc1-9d8b-f57f718572a0.png">
Anwendung des MobileNetV2-Modells auf die Daten

<a name="deployment"></a>
## 3.8 Deployment

Für das Deployment wird eine Firmware mit dem optimierten Modell auf dem Arduino Nano 33 BLE Sense gelauncht. Das ist die Edge Impulse Dokumentation für das Development: https://docs.edgeimpulse.com/docs/edge-impulse-studio/deployment

Das  Arduino Nano 33 BLE Sense Board mit dem USB Kabel an den Computer verbinden. Auf der Edge Impulse Webseite "Deployment" gehen und das Gerät “Arduino Nano 33 BLE Sense” auswählen und auf “Build” klicken:

<img width="187" alt="Bildschirmfoto 2022-09-24 um 18 34 01" src="https://user-images.githubusercontent.com/72546527/192828136-15b9027d-1c4b-4ea5-b28b-c9da971f94ee.png">

Die Firmware wird mit dem favorisierten Modell erstellt:

![Bildschirmfoto 2022-09-20 um 00 20 57](https://user-images.githubusercontent.com/72546527/192828407-5948a034-4700-4d2f-bc9f-fd8678b49069.png)

Sobald die Firmware erstellt wurde, wird sie automatisch heruntergeladen. Den Ordner öffnen und die drei Dateien “firmware-arduino-nano-33-ble-sense.ino.with_bootloader.bin”, “firmware-arduino-nano-33-ble-sense.ino.bin” und “flash_mac.command” in den Benutzerordner verschieben. Dann auf “flash_mac.command” klicken. Die neue Firmware wird auf dem Arduino Nano 33 BLE Sense Board gelauncht.

<img width="453" alt="Bildschirmfoto 2022-09-24 um 19 00 48" src="https://user-images.githubusercontent.com/72546527/192828632-6a0cb160-2c44-4355-8743-42053c8d0c10.png">

Ein neues Terminal-Fenster öffnen und folgenden Befehl eingeben: ```edge-impulse-run-impulse```
Das Arduino Nano 33 BLE Sense Board nimmt im Zweisekunden-Takt ein Bild auf und im Terminal erscheint eine Wahrscheinlichkeit, ob ein:r Fahrradfahrer:in zu erkennen ist oder nicht. Bei dem folgenden Beispiel ist der Wert 1 bei 0.73438, was aussagt, dass die Kamera eine:n Fahrradfahrer:in erkennt:

<img width="724" alt="Bildschirmfoto 2022-09-25 um 03 44 19" src="https://user-images.githubusercontent.com/72546527/192828698-fa55c9ff-5fef-446e-b108-b2ce43939732.png">

Bei dem nächsten Bild wird der Berliner Fernsehturm von der Kamera erfasst und das Modell gibt bei dem Wert 0 eine Wahrscheinlichkeit von 0.72266. Hier ist demnach kein:e Fahrradfahrer:in zu erkennen:

<img width="639" alt="Bildschirmfoto 2022-09-25 um 03 44 30" src="https://user-images.githubusercontent.com/72546527/192828758-6408466e-0197-4aeb-86f5-43b1f20a9e4b.png">

Es ist zu erwähnen, dass das Modell noch weiter optimiert werden sollte, um bessere Resultate zu erzielen. Bei anderen Bildern wurde es nicht eindeutig oder sogar falsch klassifiziert. Hinzu kommt, dass die Datenmenge gering war und es zu fehlerhaften Ergebnissen kam.

<a name="ausgabe"></a>
## 4 Ausgabe (LED-Matrix)
Bei der Erstellung unserer Anzeige war es uns wichtig, dass das Design vom Fahrrad aus erkennbar sein muss, weshalb die Frames auf das Wesentliche reduziert wurden. Unsere Anzeige zeigen, dass FarradfahrerInnen links überholen und sich rechts einordnen sollen. Jedoch die Anzeige nicht als Warnung aufgefasst werden und lediglich die FarradfahrerInnen darauf aufmerksam machen auf ihre Mitmenschen rücksicht zu nehmen. Aus diesem Grund wurden Grüntöne als Farbe für die Fahrräder gewählt.

Für die Erstellung unserer Anzeige als GIF haben wir die kostenlose Software [Piskel](https://www.piskelapp.com/p/create/sprite) genutzt, die auch als Browseranwendung verfugbar ist. Ein Tutorial zum Erstellen von GIFs mit Piskel gibt es [hier](https://www.youtube.com/watch?v=XqdZX3Ldzyo).

![](https://github.com/enisaztekin/Fahrrad-Etikette/blob/main/finalgif.gif)

<a name="learnings"></a>
## 5 Learnings

