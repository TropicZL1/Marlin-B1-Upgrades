This build of Marlin utilizes UBL to level the bed on the Ender 3 S1.

- Most options have been enabled thanks to [mriscoc](https://github.com/mriscoc)
- I've utilized mriscoc's config files to build this branch since the default example provided by the marlin team wouldn't compile with UBL enabled unless you disabled other options which was unacceptable.
- The only option not enabled is Z Auto Align since it seems you cannot define the 2nd stepper without it trying to exclude E0 however I don't think auto align is necessary given that the Z screws use a belt to spin both motors together which would prove problematic for the auto align command.
- I maintained most defaults from the config which means the mesh is a 5x5. The number of mesh points can be changed on the screen firmware, I would highly recommend using a 15x15 mesh to eliminate any issues you may encounter with your bed not being level.
- For screen firmware please use https://github.com/mriscoc/Ender3V2S1/releases make sure to download the appropriate firmware for your screen. Either Ender3S1-F4-UBL-xxxxxxxx.bin or Ender3S1-F1-UBL-xxxxxxxx.bin.
- I would also recommend manually leveling your bed at least once. Try to set the bed as level as possible, shit in shit out applies here and no matter how many mesh points you create you will never be able to get good quality prints if the bed is all jank.
- Included is a pre-compiled firmware.bin. Obviously, you may use it if you can't be bothered to compile it yourself but it's wise to just double-check my work and compile it yourself :)


Cura Start Code

```
; Ender 3 S1 Custom Start G-code
M104 S{material_standby_temperature} ; Start heating up the nozzle most of the way
M190 S{material_bed_temperature_layer_0} ; Start heating the bed, wait until target temperature reached
M109 S{material_print_temperature_layer_0} ; Finish heating the nozzle
G28                              ; Home all axes
G29 A                            ; Activate UBL
G29 L1                           ; Load mesh 1
G29 J2                           ; 4 Point tilt
G29 F10.0                        ; Fade to 10mm
G92 E0                           ; Reset Extruder
G1 Z2.0 F3000 ; Move Z Axis up little to prevent scratching of Heat Bed
G1 X0.1 Y20 Z0.3 F5000.0 ; Move to start position
G1 X0.1 Y200.0 Z0.3 F1500.0 E15 ; Draw the first line
G1 X0.4 Y200.0 Z0.3 F5000.0 ; Move to side a little
G1 X0.4 Y20 Z0.3 F1500.0 E30 ; Draw the second line
G92 E0 ; Reset Extruder
G1 Z2.0 F3000 ; Move Z Axis up little to prevent scratching of Heat Bed
G1 X5 Y20 Z0.3 F5000.0 ; Move over to prevent blob squish
```

If you experience quality issues on your prints you may need to adjust 'G29 F10.0' to a smaller number or eliminate it all together.