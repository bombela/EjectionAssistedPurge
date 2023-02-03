# EjectionAssistedPurge (EAP)

https://user-images.githubusercontent.com/216204/216499002-51c40538-1833-44a0-8337-d1b87039a244.mp4

Designed for the Voron 2.4r2. Should work on Voron Trident. Feel free modify it for other printers.
There are macros for Klipper to brush, purge, and integrate with the slicer toolchange.

The EAP can purge from 15mm³ to 185mm³. For reference, the default purge amount in PrusaSlicer and SuperSlicer is 140mm³.

It is especially useful with an automatic filament changer, like the [ERCF](https://github.com/EtteGit/EnragedRabbitProject).

# Overview

![overview](https://user-images.githubusercontent.com/216204/216500293-50a4b5c6-05cc-490f-85e6-ee62348e143c.png)

The EAP mounts on the gantry of a Voron 2.4 (or the frame for a Trident). Therefore it is always at the same Z height as the nozzle.
It is adjustable in Z and Y. Purging is done into a groove with a shape designed to hold the hot plastic, and resist warping.
The groove is covered with adhesive metallic foil to quickly cool the surface of the extrusion and prevent adhesion.

The plastic should be left to cool from 15s (ABS) to 30s (95A TPU) before ejection.
In practice, automatic filament changes takes about 30s, so even printing a single dot between swaps gives it enough time to cool.

The Grove is 1mm from the edge of the tray, and 2mm wide. This means that the tray can be as close as possible to the bed,
and only a couple milimeters past the back end of the bed. With the stock Voron 2.4r2 nothing has to be adjusted.
It is similar to reaching out to the nozzle endstop.

An ubiquitous RC Servo MG90S is used to actuate the tray against a piston and eject the solidifed plastic. There is no provision
to catch the plastic turds. They accumulate at the bottom of the printer, and this can be cleaned up from time to time.

It has been tested succesfully with ABS, PLA, PETG and 95A TPU.

# Printing, BOM, and assembly

![parts laid out for printing](https://user-images.githubusercontent.com/216204/216505215-47df2860-ccf5-4373-954d-7405d0f92bae.png)

All the [STL/STEP files](/stl) files are oriented for printing. The screenshot above shows all the parts spread out.

The design is optimized for ABS and accounts for shrinkage. I slice with arachnea, 0.2mm layer height, 0.4mm nozzle, 4 perimeters, 5 layers bottom/top, and 15% gyroid infill. Standard Voron recommended settings should also work fine.

## Bill of material

 - 2× (M5x8 + M5 tnut) for mounting the bracket on the gantry (and finely adjust in Z).
 - 2× M3x8 to lock the frame in  Y adjustment.
 - MG90S or similar RC servo 180° and the screws that come with it.
 - Aluminum adhesive tape: ~10µm thick, 20 to 40mm wide.
 - A brass brush, you know, the one we all use.
 
And for RC servo connection on the Octopus board:
 - 3×24 awg 150cm long
 - JST-XH 2pins male connector
 - JST-XH 3pins male connector
 - Optional, some wire-to-wire connector near the servo for conveniance.

## Assembly instructions

### Adhesive aluminum tape

The difficult part is the aluminum adhesive foil on the tray. Refer to the pictures for where to place the tape.

A trick is to fold it, insert it in the groove. Then gently spread it out with the shaft
of a thin screwdriver used in its whole length.
It is important for the foil to follow the contour of the grove tightly. Otherwise it will rip when the piston moves through.
Manually insert the piston and test. Redo the work if necessary, it took me 5 tries the first time.

You can extend the aluminum foil over the tray up to the rack teeth with another piece if necessary.
The large the surface area, the better the cooling.

Add a piece of adhesive aluminum tape at the tip of the piston. No need to cover the whole thing, nor the sides.
Just enough to cover the tip and prevent plastic from sticking. Finally, add one more small piece on the edge if the brush holder.
ABS tends to slide off the groove very easily and follow the nozzle. It could stick on the edge of the brush holder.

### Printed parts

Everything slides with dovetails. Install the tray first. The piston next. Slide the brush holder after that.
Insert the brush at the end, its easy to prick fingers otherwise.

Mount the pinion gear on the servo and the screw. Screw it quite tight. There is up to 1kg of force on the gear.
Then gently turn the gear into counter-clockwise (ie: as if unscrewing) until you reach the end of travel. Don't break the poor servo!

This way the gear is set for when the tray is all the way opened to the left. Place the tray all the way to the left,
the the servo. It is ok for the tray to not touch the frame when the gear engages. There is some margin on both sides.
Use the two M2 self-tapping screws that come with the servo to mount it on the frame.

Finally slide the frame into the bracket. Add the two M3 screws. Mount the bracket on the gantry. I choose to install
mine on the left side. And my [Klicky probe](https://github.com/jlas1/Klicky-Probe) on the right. I suppose you could
mirror the parts before printing. The Klipper macros are agnostic of the direction.

An easy way to install flush to the bed in Z and Y is to instruct the printer to move the toolhead to the far front and lower the nozzle on the bed.
Then the bracket can be mounted to the granty with the M5 screws and T-nut.
It is easy to adjust the Y and Z such that the top of the tray is flush with the top of the and side of the bed.
Do leave a minimum of 0.5mm from the edge of the bed. There a tiny bit of margin here, the nozzle doesn't have to be perfectly in the middle of the
grove, nor perfectly flush with the top surface of the tray.

The frame is locked in Y with the two M3 screws. No need to over-tighten there. It's threaded into plastic. 

The brush holder can be adjusted in Y independently by sliding the dovetail. For example if you want a larger gap from the bed and the tray,
you can move the brush forward a bit to get a better scrubbing of the nozzle.

### Electronics

You do need one available digital pin to control the servo. And a 5V supply.
With the Octopus board (stock Voron 2.4r2), I used PG12 on the DIAG4, and the fixed FAN7 output set for 5V.
This way, the wiring is tidy. You will need to crimp the JST connectors (listed in the BOM above).

![image](https://user-images.githubusercontent.com/216204/216509658-e682dfb8-dd4b-4776-937e-4d1976d43553.png)

Thread the wire through the Z drag chain. Follow the path of the nearest A/B motor wiring.

# Klipper macros

Include the `eap.cfg` in your Klipper configuration (most likely in `printer.cfg`).
Carefuly adjust the configuration variables at the top of the file.
Manually control your toolhead with Klipper to find out of the exact XY positions of the groove and the brush.

The macros and their parameters are documented in detail the file.

Ejection is done before purging. Maximizing the cooling time between purges.
This does wastes 2s before purging.
Klipper would need to be modified to support asyncronous actuation of the ejection.
I do not swap filament often, so for now this has not bothered me.

Here a summary of the macros:

|Macro|Description|
|-|-|
|BRUSH|Brush the nozzle. Useful in PRINT_START, PRINT_END, homing and Quand Gantry Leveling and so on.|
|PURGE|Eject, then purge and plow through the brush.|
|PURGE_HOME|Place the toolhead near the groove then eject. Used before a toolchange, see example of use in PrusaSlicer below.|
|PURGE_EJECT|Trigger an ejection. Useful for testing.|

## Use in PrusaSlicer

Here is an example of use in prusaslicer tool change gcode. When the wipe tower is turned off,
the Ejection Assisted Purge is used.

```
{if has_wipe_tower}
  T{next_extruder} ; ERCF swap { filament_preset[current_extruder] }
{else}
  PURGE_HOME ZHOP=0.2 ZCHECK=0
  T{next_extruder} ; ERCF swap { filament_preset[current_extruder] }
  PURGE VOL=150 RET=0.5 RETSPEED=35 EJECT=0 BRUSH_CLEAR=0 ZCHECK=0
{endif}
```
