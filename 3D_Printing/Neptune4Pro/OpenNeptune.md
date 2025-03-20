Open source os for neptune 4 series with better support for common enhancement like wifi over usb or webcams. As well as newer versions of klipper and co. https://github.com/OpenNeptune3D/OpenNept4une

* Installation Guide: https://github.com/OpenNeptune3D/OpenNept4une/wiki

### Installation Notes for my installation Neptune 4 Pro 
* Stepper Ver 1.2
* Mainboard Ver. 1.0

## Printer Calibration under Open Neptune
Is a little bit different then stock, see here for all possible calibration and leveling options: https://github.com/OpenNeptune3D/OpenNept4une/wiki/Printer-Calibration-%E2%80%90-Klipper-&-OrcaSlicer
### Known Problems
* Edit Configuration does not work under some firefox version

## KIAUH

A set of scripts to install and maintain klipper on an linux arm device https://github.com/dw-0/kiauh
#### Available Software
* Klipper: Controll printers by combining a general purpose computer with multiple microcontrollers and is the de facto choice for manny 3d printers https://github.com/Klipper3d/klipper
* Moonraker: Exposes an api for klipper so that frontends like fluidd can interact with klipper https://github.com/Arksine/moonraker
* Web Interfaces: Fluidd https://github.com/fluidd-core/fluidd, Mainsail https://github.com/mainsail-crew/mainsail, Octo Print https://github.com/OctoPrint/OctoPrint
* Klipper Screen: Touchscreen Gui that allow to switch between multiple printers https://github.com/KlipperScreen/KlipperScreen
* moonraker-telegram-bot: Control and monitor prints using discord without need to set up vpn https://github.com/nlef/moonraker-telegram-bot
* PrettyGcode for Klipper https://github.com/Kragrathea/pgcode
* Obico for Klipper https://github.com/TheSpaghettiDetective/moonraker-obico
* Mobilerake companion: Push Notifications for Klipper https://github.com/Clon1998/mobileraker_companion?tab=readme-ov-file#visualization-of-the-architecture
* Octo Everywhere: Similiar to Obico? https://octoeverywhere.com/?source=kiauh_readme
* OctoApp: App for Smartphone connecting to your printer https://github.com/crysxd/OctoApp-Plugin
* SimplyPrint: Cloud 3d printing

## Klipper Functions that are Configured out of the Box
* BED_LEVEL_SCREWS_TUNE
* TILTS_SCREW_ADJUST (However i adjusted the values to the ones measured from the noozle this may be wrong because in the docs it is sad to measure from the probe): 