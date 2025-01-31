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

If you do not have a native toolhead filament sensor, you can use either an inline filament sensor such as [Filatector](https://github.com/ArmoredTurtle/Filatector), or you can use a [Turtleneck buffer](https://github.com/ArmoredTurtle/Turtleneck) as a virtual toolhead endstop, please see [this guide](https://github.com/ArmoredTurtle/AFC-Klipper-Add-On/blob/main/docs/Buffer_Ram_Sensor.md) for more details.

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

After the self-test completes, you hopefully will see a picture of a happy turtle in the console log! If you get a picture of an error turtle, you may have a misconfigured AFC setting, broken pin or some other issue that needs attention.

You can display the status of sensors in Mainsail/Fluidd as regular filament switches by setting ``enable_sensors_in_gui: True`` either globally in ``AFC/AFC.cfg`` or in the individual component section (e.g. extruders in ``AFC/Turtle_1.cfg``.

If you are unable to resolve the error after visiting the [troubleshooting guide](https://github.com/thunderkeys/AFC-Klipper-Add-On/tree/main?tab=readme-ov-file#troubleshooting), you can get support from the community on the Armored Turtle discord by opening a help thread (run the Discord command ``/help``) to learn how).

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

One all four lanes are confirmed to be activating the correct extruder when the trigger switch is actuated, move on to the next step.

### Extruders
Insert filament into the feeder tube (it helps to cut it at an angle) and press through until the extruder motor gears catch the filament and load it further.  If you can press the filament through but it feels like the extruder motor is pushing back on the filmaent instead of pulling it in, try reversing the ``dir_pin`` setting for that extruder motor in ``AFC/AFC_Turtle_1.cfg``.

If you are able to load filament into all 4 lanes and get a white LED indicator, and ``AFC_STATUS`` at the console reports no errors, move on to the next step.

### TurtleNeck buffer (optional)
Test that TurtleNeck buffer is configured correctly by extending the slide all the way out, then run ``QUERY_BUFFER BUFFER=Turtle_1``.  This should return ``Trailing``.  Collapse the slide all the way so it triggers the switch at the rear, then rerun the QUERY_BUFFER command.  It should then report ``Advancing``.

Confirm proper operation of your TurtleNeck before proceeding.

## Setting up macro variables

### A note on order of operations of macros during tool changes
AFC will perform macros on each tool change (``T0``, ``T1``, etc) in the following order:

- Load sequence
  - If toolhead is loaded from another lane, that will first be unloaded (see below sequence)   
  - Poop
  - Wipe/Brush
  - Kick
  - Wipe/Brush
  - Print 
- Unload sequence
  - If toolhead cutter is enabled:
     - Cut
     - Park
  - If tip forming is enabled:
     - Park
     - Tip Form

### Uncommon variables
AFC has many variables that you can tweak for macros, including definition of complete custom macros to replace the stock ones. Most users will never need to touch these and they will not be covered in this guide. Below are the most common variables that will need adjusting for a stock BoxTurtle configuration with a Stealthburner/FilamATrix toolhead.

For more details, please refer to the AFC-Klipper-Add-On Github docs or the comments in the ``AFC/AFC.cfg`` and ``AFC/AFC_Macro_Vars.cfg`` files.

#### Park
The park macro is enabled or disabled by setting the ``park`` variable in ``AFC/AFC.cfg`` (this is prompted for during the install script, but you can change it at any time and do a firmware restart to enact the change.)

Adjust the following macro variables in ``AFC/AFC_Macro_Vars.cfg`` for the park macro (if enabled) for your specific printer.
  - ``variable_park_loc_xy`` - This is the X,Y coordinate your toolhead will park at prior to other filament changes.

#### Cut
The cut macro is enabled or disabled by setting the ``tool_cut`` variable in ``AFC/AFC.cfg`` (this is prompted for during the install script, but you can change it at any time and do a firmware restart to enact the change.)
Adjust the following macro variables in ``AFC/AFC_Macro_Vars.cfg`` for the cut macro (if enabled) for your specific printer. 
  - ``variable_retract_length`` - How much to retract filament before performing the cut. This reduces purge waste and improves reliability of insertion of the next filament. Hotend dependent, a good starting value is the length of your hotend PTFE + 1-5mm.
  - ``variable_pin_loc_xy`` - X,Y position where your toolhead cutter arm *just* touches the depressor pin.
  - ``variable_cut_direction`` - For FilamATrix, you want this to be set to ``left``. Other toolhead cutters may actuate in a different direction (e.g., Dragon Burner cutter may be ``front``).
  - ``variable_pin_park_dist`` - How far to park the toolhead near the depressor pin before initiating the move. This acts as a safety as well as helps generate momentum for the cut.
  - ``variable_cut_move_dist`` - How far the toolhead has to move from ``variable_pin_loc_xy`` to fully depress the cutter. This should be reduced from the actual value by 0.5mm as a buffer to prevent skipped steps.
  - ``variable_cut_count`` - How many times to attempt the cut. The default value is ``2``, to ensure a clean/complete cut.

#### Poop
The poop macro is enabled or disabled by setting the ``poop`` variable in ``AFC/AFC.cfg`` (this is prompted for during the install script, but you can change it at any time and do a firmware restart to enact the change.)

Adjust the following macro variables in ``AFC/AFC_Macro_Vars.cfg`` for the poop macro (if enabled) for your specific printer.
  - ``variable_purge_loc_xy`` - X,Y position where to perform the poop operation. Usuually, this is on the corner of your bed.

#### Kick
The kick macro is enabled or disabled by setting the ``kick`` variable in ``AFC/AFC.cfg`` (this is prompted for during the install script, but you can change it at any time and do a firmware restart to enact the change.)
Adjust the following macro variables in ``AFC/AFC_Macro_Vars.cfg`` for the kick macro (if enabled) for your specific printer.
  - ``variable_kick_start_loc`` - the X, Y, Z location to move to prior to begin the kick operation. If you do not want a Z move, set the Z coordinate to ``-1``.
  - ``variable_kick_direction`` - the direction to move to kick the poop off (left, right, front, back)
  - ``variable_kick_move_dist`` - how for to move in that direction to kick the poop off

#### Wipe/Brush
The wipe macro is enabled or disabled by setting the ``wipe`` variable in ``AFC/AFC.cfg`` (this is prompted for during the install script, but you can change it at any time and do a firmware restart to enact the change.)
Adjust the following macro variables in ``AFC/AFC_Macro_Vars.cfg`` for the brush macro (if enabled) for your specific printer.
  - ``variable_brush_loc`` - X, Y, Z coordinates of the center of the brush. If you do not want a Z move, set the Z coordinate to to ``-1``.
  - ``variable_y_brush`` - Set to ``true`` if you want the macro to do a brush in the Y direction first before doing X. When set to ``false``, only it will move only in the X direction.
  - ``variable_brush_width`` and ``variable_brush_depth`` - the width and depth of your brush, in mm.
  - ``variable_brush_count`` - Number of passes to make on the brush, the default is ``4``.

#### Tip forming
**NOTE**: While a toolhead cutter is recommended for best results, if you do not have one you will need to configure tip forming. The full process for this is outside the scope of this document and will be very hotend and perhaps even filament dependent. If using a 'Hub Cutter' (e.g., BT-Snappy or EREC) you will still likely need to do tip forming to prevent stringy tips from getting jammed in the toolhead, hub or BoxTurtle extruder gears.

Tip forming is enabled or disabled by setting the ``form_tip`` variable in ``AFC/AFC.cfg`` (this is prompted for during the install script, but you can change it at any time and do a firmware restart to enact the change.)

Adjust the following tip forming variables in ``AFC/AFC.cfg`` for tip forming (if enabled) for your specific printer. Note the different location that the above macros.
  - ``cooling_tube_position`` - Starting location of the cooling tube in mm (based off toolhead sensor)
  - ``cooling_tube_length`` - Length of the cooling move in mm
  - ``cooling_moves`` - number of cooling moves to perform, the default is ``4``.

### Enacting changes
Perform a ``FIRMWARE_RESTART`` (a full printer reboot is not required) after adjusting all the macro variables in ``AFC/AFC_Macro_Vars.cfg`` file.

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
