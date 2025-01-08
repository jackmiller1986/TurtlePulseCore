# BoxTurtle V1.0
BoxTurtle Automated Filament Changer (requires AFC-Klipper-Add-On (Beta))

![BT_Render](https://github.com/user-attachments/assets/c06e961f-8d1d-41ae-9c80-036669ba2657)
[![Join me on Discord](https://discord.com/api/guilds/1229586267671629945/widget.png?style=banner2)](https://discord.gg/eT8zc3bvPR)

BoxTurtle is exactly what it appears to be — an open source AMS style filament changer for [Klipper](https://klipper3d.org) machines. 
BoxTurtle requires the AFC-Klipper Add-On (found [here](https://github.com/ArmoredTurtle/AFC-Klipper-Add-On)).
The goal of BoxTurtle is to deliver a user experience as close to an AMS as possible in vanilla Klipper. i.e. an "AMS" for any klipperized printer regardless of form factor but [VORON Design](https://vorondesign.com) printers in particular.

If you appreciate the work we are doing, you can support us [here](https://www.armoredturtle.com/pages/donate).

# How it works

BoxTurtle is an automated, lane-based filament changing system, also known by some as a "Type B MMU." Each lane is equipped with its own dedicated motor that moves filament to and from the tool head independently, eliminating the need for a selector cart or servos. 

To accommodate any differences in rotation distance between the extruder in the tool head and the lane motors, BoxTurtle uses a toolhead buffer, like [TurtleNeck](https://github.com/ArmoredTurtle/TurtleNeck) by ArmoredTurtle. This system is "bufferless," meaning no filament boxes (no spaghetti boxes) are required, similar to the AMS. Each lane features an independent respooler equipped with a brushed motor, which helps rewind the spool and assists the lane motor in feeding the filament smoothly. This prevents issues like spool tangling or "bucking."

For precise PWM control of the brushed motors, BoxTurtle relies on a custom MCU, AFC-lite, developed by [Isik's Tech @xbst](https://github.com/xbst/AFC-Lite/) 

# For best results

## AFC Klipper Add-On
BoxTurtle was designed in tandem to work with the [AFC Klipper Add-On](https://github.com/ArmoredTurtle/AFC-Klipper-Add-On). For information on installing and configuring the AFC Klipper Add-On, please visit that repository for the most comprehensive detail. Most common configuration options are prompted for during the installation process.  Some of the options presented are covered below at a high level.

## Filament cutter or tip forming?
BoxTurtle works most reliably with a toolhead filament cutter. Our recommended toolhead cutter solution is [FilamATrix](https://github.com/thunderkeys/FilamATrix). Please visit that Github repository for the most up to date instructions and information. Ensure that toolhead cutting is selected during installing the AFC Klipper Add-On, or enable it manually by editing ``AFC/AFC.cfg`` and restarting Klipper.

If a toolhead filament cutter is not a possibility, tip forming is also an option. This is enabled via the installation script or by editing ``AFC/AFC.cfg`` and restarting Klipper. Tip forming is extremely dependent on your printer, filament and even environment and guidance on properly tuning this is outside the scope of this document. Please visit the [ArmoredTurtle Discord](https://discord.gg/eT8zc3bvPR) for community support on tip forming.

## Filament sensor
A pre-extruder toolhead filament sensor is recommended but not required if using a [TurtleNeck](https://github.com/ArmoredTurtle/TurtleNeck) or [TurtleNeck 2](https://github.com/ArmoredTurtle/TurtleNeck2.0) filament buffer. A post-extruder filament sensor can be defined in the AFC configurations, but is not currently used beyond reporting status of the sensor.

Options include:

- [FilamATrix](https://github.com/thunderkeys/FilamATrix)
- [Filatector](https://github.com/ArmoredTurtle/Filatector)
- [AFC Buffer Ram Sensor using TurtleNeck](https://github.com/ArmoredTurtle/AFC-Klipper-Add-On/blob/main/docs/Buffer_Ram_Sensor.md)

## Waste management

### Pooping, Kicking, and Wiping?
One option for filament purge/waste can be to have the printer 'poop' blobs of filament on the bed, have the nozzle cleaned with a brush/wipe, and hten return the toolhead near the blob and kick off the bed using kinematics into a bucket or other waste evacuation system. This works similar to the method used by [Blobifier](https://github.com/Dendrowen/Blobifier), but does not require servo actuation.  Enabling poop/kick/wipe macros can result in more reliable filament changes and color separation, at the cost of additional time during each filament swap. Each option can be enabled or disabled during the AFC installation process, or by editing ``AFC/AFC.cfg`` and restarting Klipper.

### Prime towers
Filament prime towers can be used standalone, or in combination with the above to minimize the chance that no blobs or oozes from the filament change process make it on to your final print. However, if you are using pooping as described above, you likely need far less purging than the default slicer settings suggest. You can adjust the 'flushing volumes' in most slicers, as well as the size of your prime/purge tower. A decent starting setting for these might be a flushing volume multiplier of 0.1-0.2 and a tower width size of 20mm.

If you want to rely solely on the poop purge, a static size wipe tower can be set by disabling the 'Purge into Prime Tower' setting in your slicer software's "Printer Settings".

Different filament color transitions will require different purge volumes, and thus tuning this is highly dependent on your filaments and prints you are performing.

# Enclosure

The enclosure option for BoxTurtle has been moved to its [own repository](https://github.com/ArmoredTurtle/BoxTurtle-Enclosure-). The enclosure is still under development, but the most up to date information on it can be found at that repository. Also considering [joining the ArmoredTurtle Discord](https://discord.gg/eT8zc3bvPR) to get progress updates as development progresses.

# Manual

ArmoredTurtle's project manuals are hosted [here](https://armoredturtle.xyz). There is no PDF, this is intended to make the build experience as easy as possible.
[BoxTurtle Assembly Manual](https://armoredturtle.xyz/manual-sections.html?manual=boxturtle)

# Printed Parts

If you purchased a kit, all of the parts you need to print are located in the [Base_Build](https://github.com/ArmoredTurtle/BoxTurtle/tree/main/STLs/Base_Build) folder under STLs on this repository. There is also a [web-based configurator](https://armoredturtle.xyz/stl-configurator.html) available for easy identification and download of what STLs to print outside of a base kit build.

This is NOT a [VORON Design](https://vorondesign.com) project, we strongly recommend that you run a profile that is specific to BoxTurtle. Voron parts profiles are not recommended for BoxTurtle printed parts.

There is a calibration print that you may like to print before getting started [here](https://www.printables.com/model/1004303-box-turtle-calibration-fidget).

# Wiring

![BoxTurtle_AFC-Lite_Pinout](https://github.com/user-attachments/assets/59a0e1d9-f870-4c50-a056-59aa76489e71)

Refer to [BT_Wiring/BoxTurtle_Wiring.xlsx](BT_Wiring/BoxTurtle_Wiring.xlsx) or [BT_Wiring/BoxTurtle-Wiring.md](BT_Wiring/BoxTurtle_Wiring.md) for recommended wire lengths for each lane.

# PTFE sizes/lengths

| Component | Outer Diameter | Inner Diameter | Length | Notes |
|-----------|---------------|----------------|--------|--------|
| Feeders to Trigger | 4mm | 3mm | 50mm | Quantity 4 ; use PTFE cutter jig |
| Trigger to Extruder | 4mm | 3mm | 80mm | Quantity 4 ; use PTFE cutter jig |
| Extruder to Hub (Outer lanes) | 4mm | 2mm | 171mm | Quantity 2; 3mm ID will blow out. A chamfer is recommended on each end. |
| Extruder to Hub (Inner lanes) | 4mm | 2mm | 101mm | Quantity 2; 3mm ID will blow out. A chamfer is recommended on each end. |

# Slicer configuration

[Orca Slicer](https://github.com/SoftFever/OrcaSlicer) is the preferred and recommended slicer for BoxTurtle and AFC.
## Printer Settings
![Orca_Pinter_Settings](https://github.com/user-attachments/assets/1aa56051-dbbf-49a4-b818-368e00406b17)
## Add Multiple Filaments
![Orca_Add_Filament_Settings](https://github.com/user-attachments/assets/61fb26a4-57c6-4624-8435-478d719a01ae)

## Printer Machine G-Code
```
M104 S0 ; Stops OrcaSlicer from sending temperature waits separately
M140 S0 ; Stops OrcaSlicer from sending temperature waits separately
PRINT_START EXTRUDER=[nozzle_temperature_initial_layer] BED=[bed_temperature_initial_layer_single] TOOL={initial_tool}
```
## Change Filament G-Code
```
T[next_extruder]
```
## Additional Slicer configuration - pre-OrcaSlicer 2.2.0
Configuring per-material filament ramming is no longer required as of the official OrcaSlicer 2.2.0 release (PR [#6934](https://github.com/SoftFever/OrcaSlicer/pull/6934)).  If you are on an earlier version than that (including betas/release candidates) you will need to make the following additional changes to your slicer configurations.
### Material Settings
![Orca_Material_Settings](https://github.com/user-attachments/assets/a1569e5a-24c5-48f9-98fb-26465bf7c75c)
### Ramming Settings
Because the AFC-Klipper-Add-On handles any tip forming in the extension, we need to disable these specific settings in the slicer software.  Below is a screenshot for OrcaSlicer, but most Slic3r-based slicers have a similar dialog/setting.
![Orca_Ramming_Settings](https://github.com/user-attachments/assets/2744fb86-afae-4645-9215-3f8507558509)

## Example PRINT_START macro
#### *Please note this is just an example macro to show how to incorporate the initial tool into your print start macro. Please adjust it to match your printer setup. A good starting point for a PRINT_START macro is [jontek2's "A Better PRINT_START macro"](https://github.com/jontek2/A-better-print_start-macro)*
```
[gcode_macro PRINT_START]
gcode:
  {% set BED_TEMP = params.BED|default(60)|float %}
  {% set EXTRUDER_TEMP = params.EXTRUDER|default(195)|float %}
  {% set S_EXTRUDER_TEMP = 150|float %}
  {% set initial_tool = params.TOOL|int %}

  G90 ; use absolute coordinates
  M83 ; extruder relative mode
  
  G28 # Home Printer
  # Do any other leveling such as QGL here

  AFC_PARK

  M140 S{BED_TEMP} # Set bed temp
  M109 S{EXTRUDER_TEMP} # wait for extruder temp
  T{initial_tool} #Load Initial Tool
  
  M104 S{S_EXTRUDER_TEMP} # set standby extruder temp
  M190 S{BED_TEMP} # wait for bed temp
    
  G28 Z

  # Bedmesh or load bedmesh

  AFC_PARK
  M109 S{EXTRUDER_TEMP} ; wait for extruder temp
  
  # Add any pre print prime/purge line here
  # Start Print
```


# BoxTurtle sourcing/vendors
While BoxTurtle can be mostly self-sourced, some vendors offer partial or full BoxTurtle kits. These vendors also have dedicated channels on the Armored Turtle Discord.

US:
- [DLLPDF](https://dllpdf.com) sells a [BoxTurtle frame kit](http://dllpdf.com/box-turtle-frame)
- [Isik's Tech](https://store.isiks.tech/) has BOM parts, including full kits [here](https://store.isiks.tech/collections/box-turtle-parts). Isik's Tech also ships internationally.
- [Fabreeko](https://www.fabreeko.com/products/box-turtle?_pos=4&_psq=box&_ss=e&_v=1.0)
- [KB3D](https://kb-3d.com/store/boxturtle/2132-pre-order-ldo-box-turtle-automated-filament-changer-kit-v10.html)
- [West3D](https://west3d.com/products/box-turtle-automated-filament-changer-multi-material-unit-by-armored-turtle)
  
EU:
- [Alchemy3D.de](https://alchemy3d.de) has a [BOM in a box kit](https://alchemy3d.de/products/boxturtle)
- [Lab4450.com](https://lab4450.com) resells the [AFC-Lite MCU](https://lab4450.com/product/afc-lite-board/)
- [3DO](https://3do.dk/3d-printer/2946-ldo-boxturtle-afc-kit-til-klipper-printere-forudbestilling.html)

UK:
- [OneTwo3D.co.uk](https://www.onetwo3d.co.uk)
- [Desktop Machine Shop](https://www.desktopmachineshop.com/shop/ldo-boxturtle-afc-kit-v1-0-163?category=4#attribute_values=)
  
AU:
- [DREMC](https://store.dremc.com.au/products/ldo-box-turtle-hardware-kit)
  
# Merch
- BoxTurtle T-Shirt ($3 from each sale goes to support ArmoredTurtle) via [Cotton Bureau](https://cottonbureau.com/p/QKF5XC/shirt/colored-box-turtle#/26921844/tee-men-premium-lightweight-vintage-black-tri-blend-s)- also has sweatshirt/hoodie variants
- BoxTurtle Sticker via [Dr. Mursey](https://drmursey.myshopify.com/products/box-turtle)
