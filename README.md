# watermeter-home-assistant
Watermeter with LJ12A3-4-Z/BX 5V inductive proximity sensor designed for Home Assistant

<details>
<summary>Public accountability</summary>
As I started with a watermeter sensor for Home Assistant, I wanted an ESPHome one that tracks and follows the real water meter reading exactly. Since my knowledge of ESPHome is not yet to be called extensive, I searched around. No solution fitted my idea completely, so I assembled the code you find here from numerous samples and added some of my own. Thx all for sharing!
</details>

## Used hardware
+ Wemos D1 mini (or probably any other ESP8266)
+ LJ12A3-4-Z/BX 5V inductive proximity sensor
+ 3D printed mount (or any other contraption)

You have to mount the proxity sensor the right way on your water meter. See [this page](https://www.home-assistant.io/docs/energy/water/) to start and find more mounting examples. Connect the LJ12A3 to the 5V, GND and GPIO 0 pins of the ESP8266. Change the code if ou use another GPIO pin.

### Exposed entities for Home Assistant
+ Sensors: 'Consumption', 'Flow Rate'
+ Configuration: 'Restart'
+ Diagnostic: 'IP address', 'Uptime'
+ Services: 'restore', 'set'

### Concept
The sensor 'Consumption' should follow the real water meter reading as precisely as possible, even after reboots. 'Consumption' should also be easily adjustable from Home Assistant UI. To estabish both, the code relies on two automations in Home Assistant for persistent values and an input_number and SET button in Home Assistant for adjstments.

### Setup
_**It's best to let the watermeter have a constant value during setup, so try not to use water while setting it all up.**_

+ Prepare the hardware, upload the ESPHome code and wait until it's online
+ Add the discovered device to Home Assistant
+ Add the necessary automations and helpers to Home Assistant (see homeassistant folder above)
+ Add an entities card to Home Assistant (see samples folder above)
+ 

Add the input_number and button in Home Assistant to 
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
