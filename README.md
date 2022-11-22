This build of Marlin utilizes UBL to level the bed on the Ender 3 S1.

- Most options have been enabled thanks to [mriscoc](https://github.com/mriscoc)
- I've utilized mriscoc's config files to build this branch since the default example provided by the marlin team wouldn't compile with UBL enabled unless you disabled other options which was unacceptable.
- The only option not enabled is Z Auto Align since it seems you cannot define the 2nd stepper without it trying to exclude E0 however I don't think auto align is necessary given that the Z screws use a belt to spin both motors together which would prove problematic for the auto align command.
- I maintained most defaults from the config which means the mesh is a 5x5. The number of mesh points can be changed on the screen firmware, I would highly recommend using a 15x15 mesh to eliminate any issues you may encounter with your bed not being level.
- For screen firmware please use https://github.com/mriscoc/Ender3V2S1/releases make sure to download the appropriate firmware for your screen. Either Ender3S1-F4-UBL-xxxxxxxx.bin or Ender3S1-F1-UBL-xxxxxxxx.bin.
- I would also recommend manually leveling your bed at least once. Try to set the bed as level as possible, shit in shit out applies here and no matter how many mesh points you create you will never be able to get good quality prints if the bed is all jank.
- Included is a pre-compiled firmware.bin for STM32F4 boards. Obviously, you may use it if you can't be bothered to compile it yourself but it's wise to just double-check my work and compile it yourself :)

## Flashing Mainboard Firmware

The bootloader which handles flashing new firmware on this board remembers the last filename you used.

Therefore, to flash the compiled firmware binary onto the board you must give the "`firmware.bin`" file on the SD card a unique name, different from the name of the previous firmware file, or you will be greeted with a blank screen on the next boot. The file rename is done for you when compiling with PlatformIO. Otherwise, rename the `*.bin` file accordingly.

Pay attention to the two versions of the motherboard for the S1, one is using a SoC STM32F1 and the other using a STM32F4:

 - Ender 3 printers that come with a firmware version **1.x.x** have the **STM32F1** chip.
 - Ender 3 printers that come with a firmware version **3.x.x** have the **STM32F4** chip.
 - Check the version number on the STM32Fx chip itself to confirm you have the correct version.
 - Installing 1.x.x stock firmware or a compiled one to the target **STM32F1** on **STM32F4** mainboards **might brick** them! And _vice-versa_.

Where to put the firmware file on the SDCard:

 - STM32F1 board: Put the `*.bin` file into the root folder.
 - STM32F4 board: Put the `*.bin` file into a folder named `STM32F4_UPDATE`.

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