# watermeter-home-assistant
Watermeter with a LJ12A3-4-Z/BX 5V inductive proximity sensor designed for Home Assistant

# Used hardware
+ Wemos D1 mini
+ LJ12A3-4-Z/BX 5V inductive proximity sensor (connect data to GPIO 0)

The device exposes two sensors (Consumption and
Flow Rate), a Restart button, two diagnostic fields (IP address and Uptime) and two
services ('restore' and 'set').


The intention is to let Consumption follow the real meter reading exactly.

The code is designed to work with Home Assistant and r. 

After the first boot, set Consumption to the same value as the real meter reading.
For this, service 'set' is used. Watermeter will now follow the real meter.

When Watermeter is rebooted, Consumption will be lost. Storing on the device would
require each new value to be flashed which would wear out the ESP8266 very quickly.
Instead, two automations in Home Assistant are added. The first will save the last
known value when the connection goes down (Consumption becomes unavailable). The
second automation will call service 'restore' with the last know value when the
device comes online again (Consumption becomes unknown).
     
This mechanism will not work if the device is not active while water is used, for
instance during reboot or power failures.
