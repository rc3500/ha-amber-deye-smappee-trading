# Home Assistant: Amber Electric, Deye Inverter & Smappee EV Smart Controller

A comprehensive, closed-loop Home Assistant energy trading and smart charging system. 

This package was custom-built to orchestrate wholesale energy trading (Amber Electric Australia) with a Deye hybrid inverter, a home battery, and a Smappee EV charger. It solves the complex issue of DC-coupled solar curtailment when smart-charging an EV, and provides a highly user-friendly dashboard for daily control.

## Hardware & Integrations Used
* **Energy Provider:** Amber Electric (Wholesale real-time pricing)
* **Inverter:** Deye Hybrid Inverter - SUN-10K-SG04LP3-AU
* **Battery:** AI-W5.1-B x12 (61.44kWh)
* **Inverter Connection:** [Waveshare RS485 TO WIFI/ETH](https://www.waveshare.com/wiki/RS485_TO_WIFI/ETH) adapter (for fast, local Modbus control)
* **EV Charger:** Smappee EV Wallbox
* **Dashboard:** Custom Lovelace UI (Mushroom, Power Flow Plus, Plotly, ApexCharts)

## Key Features

### 📈 Tiered Battery Dumping (Amber Electric)
Instead of hardcoding export triggers, this system uses three visual slider groups (Tier 1 to 3). 
* Set a trigger 'Sell Price' (e.g., 14c/kWh).
* Set a 'Stop Dumping' target State of Charge (e.g., 40%).
* When the feed-in tariff hits the trigger, the Deye inverter dynamically dumps power to the grid until it hits your designated battery floor, then reverts to self-consumption.

### 🛑 Automated Safeguards
* **Auto-Charge:** Automatically forces the inverter to charge the home battery from the grid if the wholesale buy price drops below $0.00.
* **Negative FiT Curtailment:** If the home battery is full and the feed-in price drops below $0.00, the system automatically commands the Deye inverter to zero-export, ensuring you never pay to export solar.

### 🚘 Closed-Loop EV Solar Tracking
Standard "open-loop" smart charging calculates available solar by subtracting house load from solar generation. However, with DC-coupled batteries, the inverter hides excess solar by storing it or curtailing the panels, causing standard EV tracking to death-spiral to zero amps. 

This system uses a **Closed-Loop Grid Zeroing Algorithm** running every 15 seconds:
* It monitors the physical grid meter and the DC battery charge rate.
* Calculation: `Net Surplus = (-1 * Grid Power) - (Battery Power * 0.96 efficiency)`.
* It leaves a **200W buffer** on the table to absorb passing clouds and inverter routing lag.
* It actively caps the EV charger to ensure the house load never breaches the Deye inverter's 9,500W hardware limit. 

### 🔋 EV Charging Modes
Controlled via a simple dashboard dropdown:
1. **Excess Solar Only:** The EV charger dynamically surfs the grid meter to soak up excess solar. The home battery capacity is actively locked via a dynamic Modbus ratchet, preventing the house battery from discharging into the car. If solar drops below 6A, the session gracefully pauses.
2. **Full Speed (Protect Battery):** The EV charges at maximum speed (bounded by the 9,500W inverter limit). It pulls all available solar and takes the balance from the grid. The home battery is locked so it does not drain into the car.
3. **Standard Charge:** Default Smappee behaviour. The EV charges at full speed, happily pulling from solar, the home battery, and the grid.

## Installation

### 1. Backend Automation Setup
1. Copy the contents of `energy_trading.yaml` into your `packages/` directory (or integrate into your `configuration.yaml`).
2. Update the entity IDs to match your specific setup (do a find and replace for `5130018050` to match your Smappee serial, and update your Deye prog entities and grid meter names).
3. **Battery Inversion Note:** In the `Solar Available for EV` sensor, the math assumes your battery sensor reports a *negative* number when charging. If your setup reports a positive number when charging, you will need to adjust the `-` to a `+` in the YAML.
4. **Smappee Setup:** Ensure your Smappee charger is permanently set to `NORMAL` mode in the official Smappee mobile app. This package bypasses Smappee's native smart charging and uses Home Assistant as the primary brain.

### 2. Dashboard Setup
To render the provided dashboards, you must install the following frontend plugins via HACS (Home Assistant Community Store):
* [Mushroom Cards](https://github.com/piitaya/lovelace-mushroom)
* [Power Flow Card Plus](https://github.com/flixlix/power-flow-card-plus)
* [Plotly Graph Card](https://github.com/dbuezas/lovelace-plotly-graph-card)
* [ApexCharts Card](https://github.com/RomRider/apexcharts-card)

Once installed, create a new dashboard, click the three dots in the top right, select "Edit in YAML", and paste the contents of either (or both) dashboard files:
* `control_dashboard.yaml` (Streamlined trading parameters and switches)
* `energy_overview_dashboard.yaml` (Complete system overview, live power flows, and forecasting charts)
