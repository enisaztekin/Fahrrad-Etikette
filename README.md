# Projektdokumentation *Road Rage war gestern*

- [1 Einführung](#einfuehrung)
    - [1.1 Motivation](#motivation)
    - [1.2 Zielstellung](#zielstellung)
    - [1.3 Vorgehensweise](#vorgehensweise)
- [2 Setup](#setup)
    - [2.1 Matrix Setup](#matrixsetup)
        - [2.1.1 Hardware](#matrixhardware)
        - [2.1.2 Software](#matrixsoftware)
    - [2.2 Personenerkennung Setup](#personenerkennung)
        - [2.2.1 Hardware](#personenerkennunghardware)
        - [2.2.2 Software](#personenerkennungsoftware)
- [3 Erkennnung von Radfahrer:innen mit TinyML](#erkennung)
    - [3.1 Verbindung zwischen Edge Impulse und Arduino Board](#edgeimpulse)	
    - [3.2 Aquisition der Daten](#aquisition)
    - [3.3 Datenaufbereitung](#datenaufbereitung)	
    - [3.4 Annotieren der Daten](#annotieren)	
    - [3.5 Daten auf Edge Impulse hochladen](#datenedge)
    - [3.6 Datenexploration](#datenexploration)
    - [3.7 Modellierung](#modellierung)
    - [3.8 Validierung](#validierung)	
    - [3.9 Deployment](#deployment)	
- [4 Ausgabe (LED-Matrix)](#ausgabe)	
- [5 Zusammenbau (Bluetooth-Signal)](#zusammenbau)
- [6 Ergänzung](#ergaenzung)
- [7 Learning](#learning)
- [8 Literaturverzeichnis](#literaturverzeichnis)
----

<a name="einfuehrung"></a>
## 1 Einführung

<a name="motivation"></a>
## 1.1 Motivation

<a name="zielstellung"></a>
## 1.2 Zielstellung

<a name="vorgehensweise"></a>
## 1.3 Vorgehensweise

<a name="setup"></a>
## 2 Setup

<a name="matrixsetup"></a>
## 2.1 Matrix Setup

<a name="matrixhardware"></a>
## 2.1.1 Hardware
Für die LED-Anzeige werden die folgenden Hardwarekomponenten benötigt.
- ESP32 Microcontroller
- 64x32 RGB LED Matrix
- 5V 3 Ampere Netzteil
- Micro-USB Kabel
- Micro-USB Kabel mit zwei offenen Kabelenden
- mindestens 15 Female-to-Female Jumper Kabel
- Terminal Block
<a name="matrixsoftware"></a>
## 2.1.2 Software
Verwendete Entwicklungsumgebung:
Arduino IDE 1.8.13

Verwendete Bibliotheken:
Adafruit GFX
ESP32-HUB75-MatrixPanel-I2S-DMA
Animated GIF

Verwendetes Beispiel:
AnimatedGIFPanel.ino

Verwendetes Plugin:
Arduino ESP32 filesystem uploader
Link, um Microcontroller in IDE zu installieren hinzufügen..
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
- Der USB-Treiber von SiLabs herunterladen und die dmg-Datei installieren: (https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers) 
2. Schritt: Arduino-IDE
- (https://www.arduino.cc/en/Main/Software) 
- Hierfür wird die Arduino IDE verwendet. Die aktuellste Version ist die Arduino IDE 2.0.0:

! <img width="907" alt="Bildschirmfoto 2022-09-20 um 03 33 51" src="https://user-images.githubusercontent.com/72546527/192511108-1a35b276-12d3-43fd-83af-44afa6645781.png">

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
## 3 Erkennnung von Radfahrer:innen mit TinyML

<a name="edgeimpulse"></a>
## 3.1 Verbindung zwischen Edge Impulse und Arduino Board

<a name="aquisition"></a>
## 3.2 Aquisition der Daten

<a name="datenaufbereitung"></a>
## 3.3 Datenaufbereitung

<a name="annotieren"></a>
## 3.4 Annotieren der Daten

<a name="datenedge"></a>
## 3.5 Daten auf Edge Impulse hochladen

<a name="datenexploration"></a>
## 3.6 Datenexploration

<a name="modellierung"></a>
## 3.7 Modellierung

<a name="validierung"></a>
## 3.8 Validierung

<a name="deployment"></a>
## 3.9 Deployment

<a name="ausgabe"></a>
## 4 Ausgabe (LED-Matrix)
design
Design muss vom Fahrrad aus erkennbar sein →  Design aufs Wesentliche reduziert
FahrradfahrerInnen sollen links überholen und sich rechts einordnen
Keine Warnung - nur als Hinweis → Grün als Farbe

Sechs Frames (GIF)
Verwendete Software: Piskel (kostenlos)

<a name="zusammenbau"></a>
## 5 Zusammenbau (Bluetooth-Signal)

<a name="ergaenzung"></a>
## 6 Ergänzung

<a name="learning"></a>
## 7 Learning

<a name="literaturverzeichnis"></a>
## 8 Literaturverzeichnis


