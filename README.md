# Home Assistant: Amber Electric, Deye Inverter & Smappee EV Smart Controller

A comprehensive, closed-loop Home Assistant energy trading and smart charging system. 

This package orchestrates wholesale energy trading (Amber Electric Australia) with a Deye hybrid inverter, a 61.44kWh home battery, and a Smappee EV charger. It solves the complex issue of DC-coupled solar curtailment when smart-charging an EV and provides a unified dashboard for daily management.

## Hardware & Integrations
* **Energy Provider:** Amber Electric (Wholesale real-time pricing)
* **Inverter:** Deye Hybrid Inverter (SUN-10K-SG04LP3-AU)
* **Battery:** 12x AI-W5.1-B (61.44kWh Total)
* **Connectivity:** RS485 to Ethernet (Modbus TCP)
* **EV Charger:** Smappee EV Wallbox
* **Vehicle:** Polestar 2

## Key Features

### 📈 Tiered Battery Dumping
Uses three adjustable tiers to manage grid exports during price spikes.
* Set a trigger **Sell Price** and a **Stop Dumping** SoC floor for each tier.
* The system dynamically toggles the Deye inverter between 'Allow Export' and 'Zero Export' based on real-time wholesale feed-in tariffs.

### 🛑 Automated Safeguards
* **Negative Buy Protection:** Automatically charges the home battery from the grid if the wholesale price drops below $0.00.
* **Solar Curtailment:** If the battery is full and feed-in prices turn negative, the system forces 'Zero Export' to ensure you don't pay to provide energy to the grid.

### 🚗 Smart EV Integration
The system uses a **Closed-Loop Grid Zeroing Algorithm** to track available solar every 15 seconds.
$$Net Surplus = (-1 \times Grid Power) - (Battery Power \times 0.96)$$
* **Excess Solar Only:** Surfs the grid meter to charge purely from surplus. Includes a 200W buffer to handle inverter lag and cloud cover.
* **Full Speed (Protect Battery):** Charges at max speed (up to 32A/9.5kW) while ensuring the home battery stays above a user-defined SoC floor.
* **Standard Charge:** Charges at full speed, allowing the home battery to assist.

## Installation

### 1. Backend Setup
1. Place `energy_trading.yaml` in your Home Assistant `packages/` folder.
2. Search and replace the Smappee serial with your own.
3. Verify your grid power and battery power sensors match the naming convention in the template sensors.

### 2. Frontend Setup
Install the following via HACS:
* Mushroom Cards
* Power Flow Card Plus
* Plotly Graph Card
* ApexCharts Card

Paste the contents of `dashboard.yaml` into a new dashboard (Raw Configuration Editor).
