# Home-Assistant-Sungrow-Modbus-Integration
Yet another Sungrow integration for Home Assistant. 

This repository wouldn't be possible without the amazing work of
- [mkaiser](https://github.com/mkaiser/Sungrow-SHx-Inverter-Modbus-Home-Assistant)
- [Louisbertelsmann](https://github.com/Louisbertelsmann/Sungrow-Wallbox-Modbus-HomeAssistant)

# Contents

This repository contains Home Assistant integration files for Sungrow hybrid inverters like SH10RT with a connected Sungrow battery like SBR. It also contains an integration file for Sungrow EV charger like AC011E.

The repository also comes with a Home Assistant dashboard file that includes plenty of cards to see entity status and metrics. The dashoard also comes woth many input settings to configure the inverter, the battery and the EV charger.

# 1. Overview

The integration uses Modbus over TCP to integrate the inverter with the battery. The AC charger integration on the other hand uses a direct Modbus connection over serial which requires some modifications on the hardware setup.

I have tested the integration with Sungrow SH10RT-V112, SBR128 and AC011E.

# 2. Capabilities

## 2.1. What's included

## 2.2. What's not included

## 2.3. What's upcoming

# 3. How to install

## 3.1. Hardware instructions

## 3.2. HomeAssistant instructions

### 3.2.1. Update secrets.yaml

### 3.2.2. Install sungrow_inverter.yaml

### 3.2.3. Install sungrow_wallbox.yaml

### 3.2.4. Install sungrow_dashboard.yaml

https://community.home-assistant.io/t/power-flow-card-plus/552326

https://github.com/ulic75/power-flow-card

