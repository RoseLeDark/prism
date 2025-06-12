# Communication Protocol / Kommunikationsprotokoll

## Overview / Überblick

This document describes the low-level communication protocol between the Arduino (host) and the ESP32 (slave) using a 10-pin parallel bus combined with an I²C control interface.

/ Dieses Dokument beschreibt das Low-Level-Kommunikationsprotokoll zwischen Arduino (Host) und ESP32 (Slave) mittels eines 10-Pin Parallelbusses kombiniert mit einer I²C-Steuerung.

---

## Hardware Interface / Hardware-Schnittstelle

- **Pins 1-4:** Data bits (4-bit parallel data bus)  
- **Pin 5:** Timing signal (Host-controlled clock signal)  
- **Pin 6:** Entry switch signal (indicates switching to next vector entry)  
- **Pins 7-10:** Additional 4 data bits (completing 8-bit parallel data bus)  

/  
- **Pins 1-4:** Datenbits (4-Bit Parallel-Datenbus)  
- **Pin 5:** Timing-Signal (Host-gesteuertes Taktsignal)  
- **Pin 6:** Entry-Switch-Signal (zeigt Wechsel zum nächsten Vektoreintrag)  
- **Pins 7-10:** Weitere 4 Datenbits (kompletter 8-Bit Parallel-Datenbus)  

---

## Protocol Description / Protokollbeschreibung

- The host (Arduino) controls all timings. The slave (ESP32) never drives the timing lines.  
- Data is transferred in vector entries; each entry is 32 bits (4 bytes), split into 8-bit chunks sent sequentially.  
- Pin 5 is held HIGH for about 2 µs when data is set on the bus, then goes LOW to signal data ready to be read.  
- Pin 6 HIGH together with Pin 5 HIGH indicates switching to the next vector entry.  
- If Pin 5 is HIGH but Pin 6 stays LOW after 4 data chunks, data will be overwritten in the same entry.  
- I²C is used to send opcodes and handle error/status communication between host and slave.  
- The I²C address 0x51 is reserved as a broadcast address to send commands to multiple devices.

/  
- Der Host (Arduino) steuert alle Timings. Der Slave (ESP32) steuert niemals die Timing-Leitungen.  
- Daten werden in Vektoreinträgen übertragen; jeder Eintrag ist 32 Bit (4 Bytes), die nacheinander in 8-Bit-Stücken gesendet werden.  
- Pin 5 wird etwa 2 µs HIGH gehalten, während Daten am Bus anliegen, und fällt dann auf LOW, um anzuzeigen, dass die Daten gelesen werden können.  
- Pin 6 HIGH zusammen mit Pin 5 HIGH signalisiert den Wechsel zum nächsten Vektoreintrag.  
- Bleibt Pin 6 nach 4 Datenstücken LOW bei Pin 5 HIGH, werden Daten im selben Eintrag überschrieben.  
- I²C wird verwendet, um Opcodes zu senden und Fehler-/Statuskommunikation zwischen Host und Slave zu ermöglichen.  
- Die I²C-Adresse 0x51 ist als Broadcast-Adresse reserviert, um mehrere Geräte gleichzeitig anzusprechen.

---

## Timing Diagram / Zeitdiagramm

Host sets data on pins 1-4 and 7-10
Pin 5 goes HIGH (approx. 2 µs)
Pin 5 goes LOW - Slave reads data
Repeat for up to 4 data chunks per entry
If Pin 6 goes HIGH with Pin 5 HIGH - switch to next entry

/ 
Host legt Daten auf Pins 1-4 und 7-10 an
Pin 5 geht HIGH (ca. 2 µs)
Pin 5 geht LOW - Slave liest Daten
Wiederhole bis zu 4 Datenstücke pro Eintrag
Wenn Pin 6 zusammen mit Pin 5 HIGH geht - Wechsel zum nächsten Eintrag


---

## Notes / Anmerkungen

- No interrupts or IRQs are used; purely polling and edge detection on timing signals.  
- The protocol is designed for simplicity, old-school hardware interfacing without dynamic memory.  
- I²C handles configuration and command/status flow separately from the parallel data bus.

/  
- Es werden keine Interrupts oder IRQs verwendet; nur Polling und Flankenerkennung an den Timingsignalen.  
- Das Protokoll ist bewusst einfach gehalten, für old-school Hardware-Anbindung ohne dynamischen Speicher.  
- I²C übernimmt Konfiguration und Kommando-/Statusfluss unabhängig vom Parallel-Datenbus.

---

# End of communication.md

