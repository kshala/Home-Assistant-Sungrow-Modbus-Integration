# Home-Assistant-Sungrow-Modbus-Integration
Yet another Sungrow integration for Home Assistant. 

This repository wouldn't be possible without the amazing work of
- [mkaiser](https://github.com/mkaiser/Sungrow-SHx-Inverter-Modbus-Home-Assistant)
- [Louisbertelsmann](https://github.com/Louisbertelsmann/Sungrow-Wallbox-Modbus-HomeAssistant)

# Contents

This repository contains Home Assistant integration files for Sungrow hybrid inverters like SH10RT with a connected Sungrow battery like SBR. It also contains an integration file for Sungrow Wallbox like AC011E.

The repository also comes with a Home Assistant dashboard file that includes plenty of cards to see entity status and metrics. The dashoard also comes with many input settings to configure the inverter, the battery and the Wallbox.

I have tested the integration with Sungrow SH10RT-V112, SBR128 and AC011E. Feedback about other hardware is very much appreciated.

# 1. Overview

The inverter and battery integration uses Modbus over TCP which requires a network connection to the inverter via Ethernet. The Wallbox integration however uses Modbus Serial to eliminate interferrence with the Inverter. This allows more control over the Wallbox using Home Assistant but requires changes to wiring on the inverter. The Wallbox Modbus pins must be removed from the Inverter plug and connected directly to your machine that runs Home Assistant. If your machine is a Raspberry PI or similar, you might have the chance to use UART pins to connect the Wallbox. I run a home server where I recommend buying a "Modbus/RS485 to USB" adapter . More information see (Hardware instructions) below.

# 2. Capabilities

This integration does some things very differently from many other integrations which allows 

## 2.1. What's included

- Wallbox event based sensor handling. Sensor updates are not read periodically but triggered by events like charging events or by changing controls.
- Wallbox single-phase and three-phase mode. This allows much more control over the charge power. Single-phase between 1380W and 3680W. Three-phase between 4140W and 11kW Note: the phase mode cannot be changed during a charge session.
- Wallbox manual mode with desired output current. Manually specify the output current at any time from 6A to 16A in 0.1A steps.
- Wallbox PV-surplus mode. The integration comes with a monitoring automation that automatically updates the output current based on available PV power.

## 2.2. What's not included

- Inverter MPPT scan. Other integrations include this. However, I observed major issues with sensors and automations related to this scan shutting down one or both of my PV strings. This is not specified in Sungrow documentations. Also I don't see a use case for running scans. With the described issues and lack of documentation I decided to not include this feature.
- Wallbox working mode. Other integrations include this sensor. Sungrow documentations specified an input register to read the working mode (Plug & Play, EMS or Network). In reality this register is not readible. Also, the wallbox has Modbus only enabled in EMS moode. Therefore this integration does not include an entity for working mode.

## 2.3. What's upcoming

- [ ] Add Inverter firmware information.
- [ ] Add Inverter alarm and system fault.
- [ ] Improve Inverter event based sensor updates.

- [ ] Add Wallbox PV surplus overnight automation.
- [x] Improve Wallbox PV surplus calculation.

- [ ] Improve dashboard. Create separate Inverter and Wallbox dashboards.
- [ ] Define better power and energy tabs for dashboards.
- [ ] Add configuration tabs for each dashboard.

# 3. How to install

Following steps are needed to install the full integration.

## 3.1. Hardware instructions

The network diagram represents how Home Assistant is connected in your home network together with the inverter and the wallbox.

![home network diagram](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.github.com/kshala/Home-Assistant-Sungrow-Modbus-Integration/setup-repo/assets/home-network-diagram.puml)

### 3.1.1. Interver

For the Sungrow Inverter you will need to use an ethernet cable and connect the LAN port of the inverter to your home network. Your DHCP server, most likely operated by your home router, will assign an IP address to your inverter. You will need to find the assigned IP address from your DHCP server / your home router.

![sh10rt modbus ethernet](./assets/sh10rt-modbus-ethernet.jpg)

### 3.1.2. Wallbox

Disclaimer: The wallbox takes more effort to connect to Home Assistant and involves disconnecting the Wallbox Modbus wires on the inverter's COM port. The steps are not difficult but always exercise caution when working on electricity or electric devices. It requires powering off your home electricity to safely perform the modification. Only continue if you know what you are doing. Consult your electrician if you want this modification done professionally. The instructions is subject to change and may be incomplete or not specific to your setup or environment. I will not provide any warranty for issues or damages. If you have any questions, feedback or concerns, feel free to reach out.

For the Sungrow Wallbox the Home Assistant integration needs a direct Modbus connection via RS485 to get full control over the Wallbox. The data communication uses two wires. You can either connect a USB dongle to your machine that runs Home Assistant or, if supported by your machine like Raspberry PI, connect the two wires to the UART pins. I use a dongle with a CH340 chip that you can get for cheap on Amazon. This is the donle I bought (no affeliate link; I have no relation to the seller or anything) https://amzn.eu/d/0b6UBwyd

Most likely your wallbox's Modbus wires are connected to the inverter's COM port. You can either cut the wires and leave loose wires connected to the inverter or disconnect the wires from the inverter's COM port. I chose the latter. The following steps only describes this approach. Below picture shows the COM connector and the pin rail with pin 2 and 4 which are the Modbus wires of the wallbox.

![sh10rt connector](./assets/sh10rt-connector.jpg)

1. Make sure to shutdown and turn off the inverter. I chose to turn off the main fuses in the breaker box to disconnect the grid and the backup from the inverter. I also chose to turn off the DC switch to disconnect all PV strings from the inverter. Last I turned off the fuse on the battery itself. That way the inverter should have absolutely no power source to stay on.
2. Once the inverter is fully turned off, disconnect the COM connector from the port. To do so you'll need to pull the latch on the connector, then pull the connector out of the inverter.
3. Be careful with the step to prevent breaking wires. Release the pin rail from the connector. The pin rail is the green part inside the connector and hold inside with another latch. Pull the latch and move the connector frame up the cable to get enough access to the connected wires.
4. The wallbox is connected to pin 2 (Data+) and pin 4 (Data-). Make notes of the wire color before disconnecting. Release the wires one by one. To release a wire, push and hold down the orange latch then carefully pull the wire. You can use tools to push the latch but be careful not to break any wires.
5. Once the two wires are released from the pin rail, ensure the cable of the two wires is released as well. In my setup that's the orange cable. I carefully removed the adhesive tape to fully remove the cable.
6. Now reassemble the COM connector and plug it back in to the inverter. Ensure the other pins (smart meter, battery, etc.) have not been affected in any way.
7. Turn back on the inverter. Make sure the DC switch and all electrical fuses are back on. In my setup I have not observed that a specific order has to be followed but I tend to start with the grid fuse, battery fuse, DC switch and then backup fuse.
8. Make sure the smart meter, battery and other devices connected to the COM connector are still functional. If you observe anything suspicious, reach out for professional support.

Now we'll connect the wires to the dongle. Connect the wire from pin 2 to the D+ pin of the dongle. Connect the wire from pin 4 to the D- pin of the dongle. Now you can connect the dongle to the Home Assistant machine.

## 3.2. Home Assistant instructions

### 3.2.1. Update secrets.yaml

### 3.2.2. Install sungrow_inverter.yaml

### 3.2.3. Install sungrow_wallbox.yaml

### 3.2.4. Install sungrow_dashboard.yaml

### 3.2.5. Install Power Flow Card Plus

This is optional. The dashboard uses a special power flow card to demonstrate the power flow of your setup. My favorite solution is Power Flow Card Plus. Check out the GitHub site for how to install.

https://community.home-assistant.io/t/power-flow-card-plus/552326
https://github.com/flixlix/power-flow-card-plus


<!--
 https://github.com/Roemer/plantuml-office 
 https://plantuml.com/de/nwdiag
 https://plantuml.com/de/stdlib
 
 -->
