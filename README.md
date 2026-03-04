# Home Assistant: Amber Electric, Deye Inverter & Smappee EV Smart Controller

A comprehensive, closed-loop Home Assistant energy trading and smart charging system. 

This package orchestrates wholesale energy trading (Amber Electric Australia) with a Deye hybrid inverter, a 61.44kWh home battery, and a Smappee EV charger. It solves the complex issue of DC-coupled solar curtailment when smart-charging an EV and provides a unified dashboard for daily management.

## Hardware & Integrations
* **Energy Provider:** Amber Electric (Wholesale real-time pricing)
* **Inverter:** Deye Hybrid Inverter (SUN-10K-SG04LP3-AU)
* **Battery:** 12x AI-W5.1-B (61.44kWh Total)
* **EV Charger:** Smappee EV Wallbox
* **Vehicle:** Polestar 2

## Key Features

### 📈 Tiered Battery Dumping
* Manages grid exports during price spikes using three adjustable tiers.
* The system dynamically toggles the Deye inverter between 'Allow Export' and 'Zero Export' based on real-time wholesale feed-in tariffs.

### 🚗 Smart EV Integration & Dynamic Battery Protection
The system uses a **Closed-Loop Grid Zeroing Algorithm** to track available solar every 15 seconds.
* **No Manual Locking Required:** The system no longer requires you to manually lock battery states.
* **Automatic Reserve Management:** The backend automatically calculates and maintains battery floors based on your `Min Battery Reserve` setting and active charging mode.
* **Excess Solar Only:** Surfs the grid meter to charge purely from surplus with a 200W safety buffer.
* **Full Speed (Protect Battery):** Charges at max speed while ensuring the home battery stays above your user-defined SoC floor.

## Installation

### 1. Backend Setup
1. Place `energy_trading.yaml` in your Home Assistant `packages/` folder.
2. Search and replace the Smappee serial with your own.
3. Verify your grid power and battery power sensors match the naming convention in the template sensors.

### 2. Frontend Setup
Install the following via HACS: Mushroom Cards, Power Flow Card Plus, Plotly Graph Card, and ApexCharts Card.
Paste the contents of `dashboard.yaml` into a new dashboard (Raw Configuration Editor).
