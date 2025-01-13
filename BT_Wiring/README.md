# BoxTurtle Wiring Guide

## Lane numbering

Lane 1 = leftmost filament lane\
Lane 4  = rightmost filament lane

## AFC-Lite Wiring guide

Prep = Trigger Filament Sensor Switches\
Load = Extruder Filament Sensor Switches

![BoxTurtle_AFC-Lite_Pinout](https://github.com/user-attachments/assets/e5673f3d-611a-4a90-8ea7-614f6e67fffc)


## Extruder Steppers
| Lane | Recommended Wire Length | Recommended Wire Gauge | Connector |
| ---- | ----------- | --------- | ------------|
| Lane 1 | 210mm | Dependent on motor | JST-XH-4 |
| Lane 2 | 320mm | Dependent on motor | JST-XH-4 |
| Lane 3 | 420mm | Dependent on motor | JST-XH-4 |
| Lane 4 | 520mm | Dependent on motor | JST-XH-4 |

##  Indicator LEDs
| Lane | Component | Recommended Wire Length | Recommended Wire Gauge | Connector |
| ---- | ----------- | --------- | ------------| --------- |
| Lane 1 | [WS2812 PCB](WS2812_PCB.png) | 130mm/130mm tail | 26-30ga | [JST-SM-M](JST-XH_JST-SM.png)/JST-SM-F |
| Lane 2 | [WS2812 PCB](WS2812_PCB.png) | 130mm/130mm tail | 26-30ga | [JST-SM-M](JST-XH_JST-SM.png)/JST-SM-F |
| Lane 3 | [WS2812 PCB](WS2812_PCB.png) | 130mm/130mm tail | 26-30ga | [JST-SM-M](JST-XH_JST-SM.png)/JST-SM-F |
| Lane 4 | [WS2812 PCB](WS2812_PCB.png) | 130mm/130mm tail | 26-30ga | [JST-SM-M](JST-XH_JST-SM.png)/JST-SM-F |
| Jumper | |  80mm | 26-30ga | JST-XH-3/JST-SM-F |

##  N20 motors for Respoolers
| Lane | Component | Recommended Wire Length | Recommended Wire Gauge | Connector |
| ---- | ----------- | --------- | ------------| --------- |
| Lane 1 | [N20 6V 500RPM](N20_6V_500RPM.png) | 205mm | 26ga | JST-XH-2 |
| Lane 2 | [N20 6V 500RPM](N20_6V_500RPM.png) | 315mm | 26ga | JST-XH-2 |
| Lane 3 | [N20 6V 500RPM](N20_6V_500RPM.png) | 415mm | 26ga | JST-XH-2 |
| Lane 4 | [N20 6V 500RPM](N20_6V_500RPM.png) | 515mm | 26ga | JST-XH-2 |

## Trigger (PREP) sensors
| Lane | Component | Recommended Wire Length | Recommended Wire Gauge | Connector |
| ---- | ----------- | --------- | ------------| --------- |
| Lane 1 | [D2F-L w/ Lever](D2F_W-Lever.png) | 155mm | 26ga | JST-XH-3 |
| Lane 2 | [D2F-L w/ Lever](D2F_W-Lever.png) | 235mm | 26ga | JST-XH-3|
| Lane 3 | [D2F-L w/ Lever](D2F_W-Lever.png) | 335mm | 26ga | JST-XH-3 |
| Lane 4 | [D2F-L w/ Lever](D2F_W-Lever.png) | 435mm | 26ga | JST-XH-3 |

## Extruder (LOAD) sensors
| Lane | Component | Recommended Wire Length | Recommended Wire Gauge | Connector |
| ---- | ----------- | --------- | ------------| --------- |
| Lane 1 | [D2HW-C201](D2HW-C201H.png) | 200mm | 26ga | JST-XH-3 |
| Lane 2 | [D2HW-C201](D2HW-C201H.png) | 310mm | 26ga | JST-XH-3 |
| Lane 3 | [D2HW-C201](D2HW-C201H.png) | 410mm | 26ga | JST-XH-3 |
| Lane 4 | [D2HW-C201](D2HW-C201H.png) | 465mm | 26ga | JST-XH-3 |

## Hub (HUB) sensor
| Component | Recommended Wire Length | Recommended Wire Gauge | Connector |
| ----------- | --------- | ------------| --------- |
| [D2HW-C201](D2HW-C201H.png) | 510mm | 26ga | JST-XH-3 |

## TurtleNeck (TN) sensors
| Component | Recommended Wire Length | Recommended Wire Gauge | Connector | Notes |
| ----------- | --------- | ------------| --------- | ---- |
| [D2F-L w/ Lever](TN_D2L_500_X2.png)  | 500mm | 26ga | JST-XH-3 | Quantity 2 |
