# Water meter sensor
Water meter with LJ12A3-4-Z/BX 5V inductive proximity sensor designed for and depending on Home Assistant

![This is an image](homeassistant/entities-card.png)

<details>
<summary>Public accountability</summary>
<i>As I started with a water meter sensor for Home Assistant, I wanted an ESPHome device that shows the real water meter reading exactly. Since my knowledge of ESPHome is not yet to be called extensive, I searched around. No solution fitted my idea completely, so I assembled the code that you will find here from numerous samples and added some of my own. Thx all for sharing!</i>
</details>

## Hardware
+ Wemos D1 mini (or probably any other ESP8266)
+ LJ12A3-4-Z/BX 5V inductive proximity sensor
+ 3D printed mount (or any other contraption)

You have to mount the proxity sensor the right way on your water meter. See [this page](https://www.home-assistant.io/docs/energy/water/) to start and find more mounting examples. Connect the LJ12A3 to the 5V, GND and GPIO 0 pins of the ESP8266. Change the code if you use another GPIO pin.

### Exposed entities
+ Sensors: `Consumption`, `Flow Rate`
+ Configuration: `Restart`
+ Diagnostic: `IP address`, `Uptime`
+ Services: `restore`, `set`

### Concept
The value of `Consumption` should represent the real water meter reading as precisely as possible, even after reboots or power failures. `Consumption` should also be easily adjustable from the Home Assistant UI. To establish both, the ESPHome code relies on three automations, two input_numbers and one input_button in Home Assistant. Two automations and one input_number to preserve `Consumption` during reboots, one input_number and input_button to adjust `Consumption`.

### Setup
***It's best to let the watermeter have a constant value during setup, so try not to use water while setting it all up.***

+ Prepare the hardware, upload the ESPHome code and wait until it's online
+ Add the discovered device `watermeter` to Home Assistant
+ Add the necessary automations and helpers to Home Assistant
+ Add an entities card to Home Assistant
+ Set `New Consumption` to the real water meter reading, press `Set New Consumption` and use some water to confirm.
+ Press `Reboot device` and use some water to confirm.

**TO DO:** _'Consumption' has the correct value but is shown as_ `unknown` _right after boot, some water use is necessary to get the first reading. Haven't sorted this out yet, ideas are welcome!_

### Energy Dashboard
As soon as the sensor comes online for the first time, Home Assistant will start collecting statistics. Energy Dashboard uses these values but because the first value of 'Consumption' probably will not be the correct meter reading, Energy Dashboard starts on the wrong foot. It will report a very large water consumption in the first hour. I have found no way to correct that in Home Assistant. But this seems to work:

1. Restart Home Assistant before you add (configure) the integration for this device.
2. Do the setup as described above. Be sure to leave your water meter with the correct value for 'Consumption' and do no use water yet.
3. Remove the device from Home Assistant, wait a minute and restart Home Assistant.
4. Search under _Settings_ => _Devices & Services_ => _Entities_ for `watermeter` and confirm all device related entities are gone (helpers and automations are still there).
5. Search under _Developer Tools_ => _Statistics_ for `watermeter` and confirm all device related entities are gone[^1].
6. Add the device intergration to Home Assistant once again.
7. Add `sensor.watermeter_consumption` as a water source to the Energy Dashboard. It can take a few minutes before the first statistics arrive.
8. Energy Dashboard should now report correct water consumption.

### Usage
The mechanism to retain the correct 'Consumption' will not work if the device is not active while water is used, for instance during reboot or power failures. After a while 'Consumption' could be lagging. Check the real meter reading once in a while and correct 'Consumption' if necessary.

[^1]: If these values are not gone ***AND you're very familiar with editing the Home Assistant database*** you could delete the corresponding rows from tables `statistics_meta` (find the `metadata_id`'s here), `statistics` and `statistics_short_term` (use the `metadata_id`'s for these two tables to find the rows). ***Shutdown Home Assistant gracefully and backup the database before editing!***.
