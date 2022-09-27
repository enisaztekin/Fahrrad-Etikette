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

<a name="matrixsoftware"></a>
## 2.1.2 Software

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

3. Schritt: Arduino Nano 33 BLE Board auswählen + Libraries installieren
- Board “Arduino Mbed OS Nano Boards” installieren:

- Drei Libraries installieren:

4. Schritt: Beispielcode 
- Es gibt bereits einen Beispielcode, um die Kamera zu testen.
- File > Examples > Harvard_TinyMLx > test_camera




- Das Board “Arduino Nano 22 BLE” auswählen:

- Verify
- Upload:

- Serial Monitor öffnen. 
- “Both NL & CR” und 115200 baud” auswählen.
- Befehle:
    - single + CMD + ENTER (Einzelbild wird von der Kamera erstellt und für jedes Pixel wird eine Hexadezimalzahl ausgegeben.)
    - live + CMD + ENTER (Die Rohbytes der Bilder werden live über die serielle Verbindung.)
    - capture + CMD + ENTER (Löst im Einzelmodus eine Bildaufnahme aus.) 
- Ausgabe bei den Befehlen single und dann capture:

- Das Ergebnis zeigt, dass die Kamera funktioniert. Jedoch können im Serial Monitor keine Zeilen kopiert werden. Der Aufwand wäre daher zu hoch, um die Hexadezimalzahlen zu entnehmen und abbilden zu lassen.

<a name="personenerkennungsoftware"></a>
## 2.2.2 Software

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

<a name="zusammenbau"></a>
## 5 Zusammenbau (Bluetooth-Signal)

<a name="ergaenzung"></a>
## 6 Ergänzung

<a name="learning"></a>
## 7 Learning

<a name="literaturverzeichnis"></a>
## 8 Literaturverzeichnis


