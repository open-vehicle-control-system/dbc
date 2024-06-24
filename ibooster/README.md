# OVCS - I Booster Gen 2 Tesla Model 3 RHD


You will find the DBC file [here](https://github.com/open-vehicle-control-system/dbc/blob/main/ibooster/ibooster_gen_2_tesla_model_3_right_hand_drive.dbc).

## Operation principles:

* The I Booster Gen 2 is an electric power brake system used in a lot of different cars. In this specific case we are using a refurbished Testla model 3 RHD.
* The I Booster module is connected to two CAN networks: Yaw and Vehicle.
* You have to send the 0x38B, 0x38C and 0x38D frames on the Yaw network every 10ms before powering up the I Booster to make it work.
* Once started, you have to set the "external_request" flags on `0x38C` **and** `0x38B` in order to control the brakes (And remove it to stop).
* The `flow_rate` signal on the `0x38C` frame allows you to control how fast you want to actuate the brakes, il will brake up to 100% unless you set it back to `32256` which is the zero point.
* You have to send a value below the zero point to release the brakes.
* You can then monitor the rod position on `0x39D` to determine when to stop.

## Known issues

* The `alert_matrix` frame (`0x35D`) is reporting an invalid vehicle speed, some flags are probaly missing/wrong in the `0x38B`, `0x38C` or `0x38D` frames.
* `0x38E` and `0x38F` are also emitted by the IBooster. Some interesting fault flags are probably available there but not reversed yet.

## Implementation

* A (WIP) concrete implementation can be found [here](https://github.com/open-vehicle-control-system/ovcs/blob/main/vms/core/lib/vms_core/bosch_controllers/ibooster_gen2.ex)

## Credit:

The following sources have been used for this reverse engineering:

* Traces from the OpenInverter forum: https://openinverter.org/forum/viewtopic.php?t=4997
* Implementation for the I Booster Gen 1 of Ocelot: https://github.com/RetroPilot/ocelot/blob/main/firmware/ibst/main.c
* Tesla Model 3 DBC:
  * https://github.com/commaai/opendbc/blob/master/tesla_model3_party.dbc
  * https://raw.githubusercontent.com/thezim/DBCTools/master/Samples/tesla_model3.dbc
* Wiring details: https://www.evcreate.com/wiring-the-ibooster/
