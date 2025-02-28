## Bed Leveling
### Manual Leveling
[https://youtu.be/Ze36SX1xzOE?si=k7DXD_LqPeN3a4Ox](https://youtu.be/Ze36SX1xzOE?si=k7DXD_LqPeN3a4Ox)

- Manual leveling by deactivating t and x Axis move to each screw and do paper test before bring bed to Print Temperatur
    
- Make bed. Level Test Print and it you See errors during Print you can try to live adjust if you have a clear view on the noozle you can live adjust
    
- Pearled filament = noozle too far away/ rillen = noozle too close
### Advanced leveling with Tilt Screw Adjust
* Tilt Screw Adjust is a macro that can be but into klippers printer.cfg config and automatically calculate how to turn the bed screws for an equally leveled bed 
* Tutorial i used:  https://www.kevink.org/klipper-bed-leveling-with-screws-tilt-adjust-and-bltouch-probe/
* Edit `printer.cfg` and add this config section
```
[screws_tilt_adjust]

horizontal_move_z: 10

screw1: 114.00, 111.00

screw1_name: center_mount

screw2: 28.65, 28.85

screw2_name: bottom_left

screw3: 200.65, 28.85

screw3_name: bottom_right

screw4: 28.65, 196.85

screw4_name: top_left

screw5: 200.65, 196.85

screw5_name: top_right
```
* In addition to the tutorial provided this will take the center as base and all screws should be adjusted relative to this. This is a specific problem for the neptune 4 pro because there is no center screw that can be changed as suggested on reddit here https://www.reddit.com/r/ElegooNeptune4/comments/185ia08/neptune_4_max_users_heres_what_you_need_to_use/
* To calibrate you just have to run `G28` and `SCREWS_TILT_CALCULATE` and adjust according to the provided values
* How to interpret the values returned can be read here basically considering a clock this is how turns need to be done https://www.klipper3d.org/Manual_Level.html#adjusting-bed-leveling-screws-using-the-bed-probe
### Problems with Bad Leveling and how to solve:
* Layer Squish[](https://ellis3dp.com/Print-Tuning-Guide/articles/first_layer_squish.html)[https://ellis3dp.com/Print-Tuning-Guide/articles/first_layer_squish.html](https://ellis3dp.com/Print-Tuning-Guide/articles/first_layer_squish.html)

## Belt Tension
TBD
## Steps per MM
TBD

## E Step Calibration

https://ellis3dp.com/Print-Tuning-Guide/articles/extruder_calibration.html

## Axis Limits
These are the real physical Limits of your printer, wrong configuration can lead to physical problems or prints not being applied on the correct coordinates (worst case not printing on the bed)
https://github.com/rootiest/zippy_guides/blob/main/guides/axis_limits.md