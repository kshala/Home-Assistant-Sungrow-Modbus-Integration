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
- [ ] Improve Wallbox PV surplus calculation.

# 3. How to install

Following steps are needed to install the full integration.

## 3.1. Hardware instructions

For the Sungrow Inverter you will need to use an ethernet cable and connect the LAN port of the inverter to your home network.

![home network diagram](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.github.com/kshala/Home-Assistant-Sungrow-Modbus-Integration/setup-repo/assets/home-network-diagram.puml)

![sh10rt modbus ethernet](./assets/sh10rt-modbus-ethernet.png)

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
