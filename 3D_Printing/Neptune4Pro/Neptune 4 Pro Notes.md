
## Leveling

Coordinates Measures from the Noozle
* Center Mount: (X,Y) = (114.00;111.00)
* Bottom Left: (X,Y) = (28,65;28,85)
* Bottom Right: (X, Y) = (200,65;28,85)
* Top Left: (X,Y) = (28,65;196,85)
* Top Right. (X, Y) = (200,65;196,85)

Coordinates Measured from the Probe (As from reddit )
* Bottom Left: 55, 10
* Bottom Right: 225, 10
* Top Left 55, 180
* Top Right 225 , 180

How to level:

* Manueller Papiertest
Klipper Mesh interpretieren: 
* Positive Werte bedeuten, dass der Druckkopf zu nah an der Plattform ist also die Plattform nach unten Bewegt werden muss
* Negative Werte bedeuten, das der Druckkopf zu weit weg von der Platform ist also die Plattform hoch bewegt werden muss
--> Manchmal ist es auch anders herum aber am besten ist man schaut sich das Mesh an und Führt den Papiertest dann an einer ecke mit dem extremsten Wert durch, dann findet man relativ schnell heraus welcher Fall zutrifft
## Flow rate 

[Flow Rate Calibration in OrcaSlicer: A Comprehensive Guide | Obico Knowledge Base](https://www.obico.io/blog/flow-rate-calibration-orca-slicer-comprehensive-guide/)

### Wlan Setup 

* https://3d-druck-erklaert.de/elegoo-neptune-4-pro-mit-wlan-ausruesten &Rightarrow; Unfortunately this only worked once and then the recommended turning off and on again did not fix the problem
* https://www.obico.io/blog/how-to-connect-your-elegoo-neptune-4-pro-to-wifi/ &Rightarrow; Needs to be tested

## Orca Slicer Machine GCODE with adaptive leveling

```
;ELEGOO NEPTUNE 4 PRO
M220 S100 ;Set the feed speed to 100%
M221 S100 ;Set the flow rate to 100%
M104 S140
M190 S[bed_temperature_initial_layer_single]
G90
BED_MESH_CLEAR
G28 ;home
BED_MESH_CALIBRATE mesh_min={adaptive_bed_mesh_min[0]},{adaptive_bed_mesh_min[1]} mesh_max={adaptive_bed_mesh_max[0]},{adaptive_bed_mesh_max[1]} ALGORITHM=[bed_mesh_algo] PROBE_COUNT={bed_mesh_probe_count[0]},{bed_mesh_probe_count[1]} ADAPTIVE=0 ADAPTIVE_MARGIN=0
G1 Z10 F300
G1 X67.5 Y0 F6000
G1 Z0 F300
M109 S[nozzle_temperature_initial_layer]
G92 E0 ;Reset Extruder
G1 X67.5 Y0 Z0.4 F300 ;Move to start position
G1 X167.5 E30 F400 ;Draw the first line
G1 Z0.6 F120.0 ;Move to side a little
G1 X162.5 F3000
G92 E0 ;Reset Extruder
```