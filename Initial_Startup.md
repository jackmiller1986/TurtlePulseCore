# BoxTurtle Initial Startup/Commissioning guide

The following guide will help you on the journey to multicolor printing after you have physically completed the [assembly of your BoxTurtle](https://armoredturtle.xyz).

## Ensure minimum system requirements
The AFC Klipper Add-On requires a minimum Klipper/Kalico version of 0.12.0, as well as a correspoding klippy-env python environment at least 3.x.

If you are on Klipper/Kalico 12, but your ~/klippy-env/bin/python returns version 2.7.x, you can recreate it with the following.

```
sudo service klipper stop

mv ~/klippy-env ~/klippy-env2.7
virtualenv -p python3 ~/klippy-env
~/klippy-env/bin/pip install -r ~/klipper/scripts/klippy-requirements.txt

sudo service klipper start
```

After recreating it, you may need to reinstall any custom add-ons, such as Klippain Shake&Tune, TMC Autotune, etc.

Ensure you have a clean, functioning klipper install with all of these minimum requirements met before proceeding to the next step.

## Flashing the AFC-Lite board

The AFC-Lite MCU can be connected with either CANBUS or USB.

If you choose to use CANBUS, follow [this guide from Esoterical](https://canbus.esoterical.online/toolhead_flashing/common_hardware/AFC-Lite/README.html).  This site also has a wealth of information on how to configure CANBUS if this is your first time using it.

If you choose to use USB, you will still need to run a 24 V+/V- cable to the AFC-Lite canbus port, just leave the CAN High/Low pins unpopulated. To flash the AFC-Lite for use with USB for data, follow [this guide from Esoterial](https://usb.esoterical.online/hardware_config/STM32/AFC_Lite.html)

Further details regarding the AFC-Lite can be found [in the AFC-Lite manual](https://github.com/xbst/AFC-Lite/blob/master/Docs/AFC-Lite_Manual.pdf).

After flashing and setting up connections/configurations appropriately, you should be able to either obtain the CANBUS UUID (if using CAN) or the device serial path (e.g., ``/dev/serial/by-id/...``) (if using USB) for the AFC-Lite MCU.  Please ensure you have these values before you proceed, as they will be required.

## Make note of any toolhead sensor pins
If you are using [FilamATrix](https://github.com/thunderkeys/FilamATrix), and are using toolhead endstop sensors, make a note of what MCU pins those sensors are connected for the pre-extruder gear sensor (aka ``pin_tool_start``) and post-extruder gear sensor (``pin_tool_end``). Use these in the next step to properly install and configure AFC.

## Install the AFC Klipper Add-On
BoxTurtle works best with the [AFC Klipper Add-On](https://github.com/ArmoredTurtle/AFC-Klipper-Add-On). Follow the instructions on that Github for installation and configuration.

Make sure you update the following settings:
- ``canbus_uuid`` if using CANBUS
- ``serial`` if using USB
- ``pin_tool_start`` and/or ``pin_tool_end``

For best results, reboot your printer after installing the Add-On and including it in your printer.cfg.  This will ensure all required modules are enabled.

## First self-test
AFC will run a self-test automatically on startup. It will run through the respoolers on each lane (from left to right, lanes 1-4) and will update the lightbox LED indicating status for each lane.

The default status indicators are:

- Red: No filament loaded/detected at extruder sensor
- White: Filament loaded to lane extruder sensor
- Blue: Filament loaded to toolhead

After the self-test completes, you hopefully will see a picture of a happy turtle in the console log! If you get a picture of an error turtle, you may have a misconfigured AFC setting, broken pin or some other issue that needs attention. If you are unable to resolve the error after visiting the [troubleshooting section](link here), you can get support from the community on the Armored Turtle discord by opening a help thread (run the Discord command ``/help``) to learn how).

## Further tests

### Respoolers
Run the ``TEST`` command against each lane (one at a time) to verify proper respooler operation:
- ``TEST LANE=lane1``
- ``TEST LANE=lane2``
- ``TEST LANE=lane3``
- ``TEST LANE=lane4``

Verify that each respooler works properly before proceeding.

### Trigger switches
Actuating the trigger switch should begin pulsing that lane's extruder motor to load filament.  Verify that the switch being actuated is activating that same lane's extruder motor.

### Extruders
Insert filament into the feeder tube (it helps to cut it at an angle) and press through until the extruder motor gears catch the filament and load it further.  If you can press the filament through but it feels like the extruder motor is pushing back on the filmaent instead of pulling it in, try reversing the ``dir_pin`` setting for that extruder motor in ``AFC/AFC_Turtle_1.cfg``.

If you are able to load filament into all 4 lanes and get a white LED indicator, and ``AFC_STATUS`` at the console reports no errors, move on to the next step.

### TurtleNeck buffer (optional)
Test that TurtleNeck buffer is configured correctly by extending the slide all the way out, then run ``QUERY_BUFFER BUFFER=Turtle_1``.  This should return 'Trailing``.  Collapse the slide all the way so it triggers the switch at the rear, then rerun the QUERY_BUFFER command.  It should then report ``Advancing``.

Confirm proper operation of your TurtleNeck before proceeding.

## Setting up macro variables

### Park
- TBD

### Cut
- TBD

### Poop
- TBD

### Kick
- TBD

### Wipe
- TBD

Perform a ``FIRMWARE_RESTART`` after adjusting all the macro variables in ``AFC/AFC_Macro_Vars.cfg`` file.

## Calibration
Follow [this guide](https://github.com/thunderkeys/AFC-Klipper-Add-On/blob/main/docs/Calibration.md) to calibrate the necessary lengths of your BoxTurtle.

## First load of filament to toolhead

You're now ready to attempt your first Automated Filament Control action!

- Home your printer (using G28 or Mainsail/Fluidd GUI buttons)
- Heat the hotend to your filament's extrusion temperature (e.g., PLA 210, ABS 260, etc).
- Load filament the filament for lane 1 with with the ``T0`` command. Confirm that filament travels to the toolhead and performs any configured POOP or WIPE actions.  You should now also be able to manually extrude filament using the console/GUI.
- Change filament to lane 2 using the ``T1`` command.  Confirm that the toolhead moves to the park position, performs the cut (or tip shaping, if not using a cutter), retracts the filament for lane 1 back to the BoxTurtle hub.  It will then load lane 2's filament to the toolhead, and perform the POOP, WIPE, KICK and WIPE macros.
- Repeat this process for lane 3 (``T2``) and lane 4 (``T3``).

- If you are able to successfully load and unload filament without intervention, you are ready to move on to the next step. Almost there!

### Configuring your slicer

- TBD

### Time for cereal
If you have completed all of the above, you are ready to attempt a BoxTurtle serial (cereal) print.  The latest information on the serial print can be found on the Armored Turtle discord in the [\#serial-info](https://discord.com/channels/1229586267671629945/1282095413046022214) channel.
