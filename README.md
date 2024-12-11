# BoxTurtle V1.0
BoxTurtle Automated Filament Changer (requires AFC-Klipper-Add-On (Beta))

![BT_Render](https://github.com/user-attachments/assets/c06e961f-8d1d-41ae-9c80-036669ba2657)
[![Join me on Discord](https://discord.com/api/guilds/1229586267671629945/widget.png?style=banner2)](https://discord.gg/eT8zc3bvPR)

BoxTurtle is exactly what it appears to be — an open source AMS style filament changer for [Klipper](https://klipper3d.org) machines. 
BoxTurtle requires the AFC-Klipper Add-On (found [here](https://github.com/ArmoredTurtle/AFC-Klipper-Add-On)).
The goal of BoxTurtle is to deliver a user experience as close to an AMS as possible in vanilla Klipper. i.e. an "AMS" for any klipperized printer regardless of form factor but [VORON Design](https://vorondesign.com) printers in particular.

If you appreciate the work we are doing, you can support us [here](https://www.armoredturtle.com/pages/donate).

# How it works

BoxTurtle is an automated, lane-based filament changing system, also known by some as a "Type B MMU." Each lane is equipped with its own dedicated motor that moves filament to and from the tool head independently, eliminating the need for a selector cart or servos. Synchronization between the lane motor and the tool head occurs when the filament triggers a sensor (such as [FilaTector](https://github.com/ArmoredTurtle/Filatector)) that is located in or near the tool head.

To accommodate any differences in rotation distance between the extruder in the tool head and the lane motors, BoxTurtle uses a toolhead buffer, such as the [Belay by Annex Engineering](https://github.com/Annex-Engineering/Belay) or the [TurtleNeck](https://github.com/ArmoredTurtle/TurtleNeck) by ArmoredTurtle. This system is "bufferless," meaning no filament boxes (no spaghetti boxes) are required, similar to the AMS. Each lane features an independent respooler equipped with a brushed motor, which helps rewind the spool and assists the lane motor in feeding the filament smoothly. This prevents issues like spool tangling or "bucking."

For precise PWM control of the brushed motors, BoxTurtle relies on a custom MCU, AFC-lite, developed by [Isik's Tech @xbst](https://github.com/xbst/AFC-Lite/)  While the system currently supports BTT MMB CAN, this is a temporary solution to facilitate broader testing and will not be a permanent feature.
# Enclosure

The enclosure option for box turtle has been moved to it's [own repository](https://github.com/ArmoredTurtle/BoxTurtle-Enclosure-).

# Manual (WIP)

Armored Turtle's project manuals are hosted [here](https://armoredturtle.xyz). There is no PDF, this is intended to make the build experience as easy as possible.
[Will update this as progress is made](https://armoredturtle.xyz)

# Printed Parts

If you purchased a kit, all of the parts you need to print are located in the [Base_Build](https://github.com/ArmoredTurtle/BoxTurtle/tree/main/STLs/Base_Build) folder under STLs on this repository. There is also a [web-based configurator](https://armoredturtle.xyz/stl-configurator.html) available for easy identification and download of what STLs to print outside of a base kit build.

This is NOT a [VORON Design](https://vorondesign.com) project, we strongly recommend that you run a profile out specific to BoxTurtle. Voron parts profiles are not recommended.

There is a calibration print that you may like to print before getting started [here](https://www.printables.com/model/1004303-box-turtle-calibration-fidget).

# Wiring

![BoxTurtle_AFC-Lite_Pinout](https://github.com/user-attachments/assets/59a0e1d9-f870-4c50-a056-59aa76489e71)


Refer to [BT_Wiring/BoxTurtle_Wiring.xlsx](BT_Wiring/BoxTurtle_Wiring.xlsx) or [BT_Wiring/BoxTurtle-Wiring.md](BT_Wiring/BoxTurtle_Wiring.md) for recommended wire lengths for each lane.

# PTFE sizes/lengths

| Component | Outer Diameter | Inner Diameter | Length | Notes |
|-----------|---------------|----------------|--------|--------|
| Feeders to Trigger | 4mm | 3mm | 50mm | Quantity 4 ; use PTFE cutter jig |
| Trigger to Extruder | 4mm | 3mm | 80mm | Quantity 4 ; use PTFE cutter jig |
| Extruder to Hub (Outer lanes) | 4mm | 2mm | 160mm | Quantity 2; 3mm ID will blow out. A chamfer is recommended on the extruder end. |
| Extruder to Hub (Inner lanes) | 4mm | 2mm | 85mm | Quantity 2; 3mm ID will blow out. A chamfer is recommended on the extruder end. |

# Slicer configuration

[Orca Slicer](https://github.com/SoftFever/OrcaSlicer) is the preferred slicer for BoxTurtle use.
## Printer Settings
![Orca_Pinter_Settings](https://github.com/user-attachments/assets/1aa56051-dbbf-49a4-b818-368e00406b17)
## Material Settings
![Orca_Material_Settings](https://github.com/user-attachments/assets/a1569e5a-24c5-48f9-98fb-26465bf7c75c)
## Ramming Settings
![Orca_Ramming_Settings](https://github.com/user-attachments/assets/2744fb86-afae-4645-9215-3f8507558509)
## Add Multiple Filaments
![Orca_Add_Filament_Settings](https://github.com/user-attachments/assets/61fb26a4-57c6-4624-8435-478d719a01ae)

## Printer Machine G-Code
```
M104 S0 ; Stops OS from sending temp waits separately
M140 S0
PRINT_START EXTRUDER=[nozzle_temperature_initial_layer] BED=[bed_temperature_initial_layer_single] TOOL={initial_tool}
```
## Change Filament G-Code
```
T[next_extruder]
```
## Example PRINT_START macro
#### *Please note this is just an example macro to show how to incorporate the initial tool into your print start macro. Please adjust it to match your printer setup.*
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
