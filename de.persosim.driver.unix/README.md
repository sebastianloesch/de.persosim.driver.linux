# PersoSim - Der Open Source Simulator für den elektronischen Personalausweis
Stand: 02.06.2016

## Systemvorraussetzungen
Bisher getestet unter Ubuntu 12.04 LTS und OS X 10.10

### Ubuntu 12.04 LTS
Benötigt werden zusätzlich zur Standardinstallation die folgenden Pakete
- pcscd
- pcsc-tools
- libpcsclite-dev

Diese können per `sudo apt-get install pcscd pcsc-tools libpcsclite-dev` installiert werden.

### OS X 10.10
Benötigt wird das Paket XCode, welches über den Mac App Store installiert werden kann.

## Installation
Das Treiberpaket liegt momentan lediglich im Quellcode vor und kann mit der weit verbreiteten Kommandokombination
```
cmake .
make
sudo make install
```
kompiliert und installiert werden.

# Sonstiges
Solange kein Simulator läuft, meldet der Treiber "Keine Karte im Leser". Sobald ein Simulator am konfigurierten Port verfügbar ist, ist dieser über den Treiber nutzbar.