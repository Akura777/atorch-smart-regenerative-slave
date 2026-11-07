# atorch-smart-regenerative-slave
Custom hybrid energy recovery / regenerative slave module and 3D printed SFF enclosure for ATORCH BW600 electronic loads.
Atorch Smart Regenerative Slave Module (DIY)
A high-efficiency custom Smart Regenerative Slave Module and deep power stage upgrade for electronic loads with expansion support (Atorch BW600 series and equivalents).

Instead of wasting energy from tested power sources as heat, this module intercepts control signals and reroutes power to a DC-DC converter to return energy back into a buffer battery or a portable power station, while completely maintaining the master unit's linear load capabilities up to 500W+.

🌟 Key Features
Smart Slave Architecture: A custom regenerative sub-module that integrates via the native Atorch multi-module expansion protocol using the CV analog control line.
Power Switch Integration: The LM393 comparator reads the external analog CV signal from the Atorch master and functions as a high-efficiency electronic switch (Power Gate / Switch) on the low-side ground break.
Deep Power Stage Upgrade: Stock MOSFETs are replaced with genuine brand-name keys, shifting to heavy-duty PC cooling hardware (low-profile CPU heatsinks with copper heatpipes).
Versatility & Cost-Efficiency: The circuit operates completely independently of the internal DC-DC converter protections and buffer chemistry. You can customize the build using any available parts.
📊 Operating Principle & Topology
1. Input Stage (Low-Side Switch)
Installed in the low-side break of the negative wire right before the DC-DC converter.
Utilizes two powerful N-channel CS120N10 MOSFETs (100V, 120A, TO-220) connected in parallel to achieve ultra-low internal resistance.
Gate drive is controlled by an LM393 comparator board. The activation threshold is tuned via a blue precision potentiometer strictly to 0.1V or higher on the Atorch CV line to eliminate ghost noise and semiconductor leakage during standby.
Normal Operation: As soon as the Atorch master board outputs a working potential to the CV bus, the comparator instantly applies +12V to the gates of the CS120N10 power switches. The MOSFETs open fully ("to the floor"), letting current flow into the DC-DC converter. The master unit perfectly tracks Ah and Wh via its stock current shunt.
💡 The Dynamic Feedback Hack
This setup relies on the stock Atorch multi-module algorithm, which is originally designed to balance current across 20–30 parallel linear slave boards. The master unit remains blind to the modification:

You set a target discharge current on the Atorch master (e.g., 15A).
The master raises the CV bus voltage (e.g., up to 3V) to open its internal MOSFETs.
The LM393 senses the voltage rising above 0.1V. Powered through its onboard 10kΩ pull-up resistor, it snaps the CS120N10 switches wide open, linking the DC-DC converter (Riden or equivalent) to the circuit.
The DC-DC converter begins pulling its configured current (e.g., 12A) directly from the shared drain junction.
The Atorch master senses this 12A load through its internal measuring shunt. To maintain its 15A target and prevent overcurrent, its internal feedback loop automatically drops the CV bus voltage down to around 0.2V, closing its linear stage slightly.
At 0.2V, the master's built-in linear MOSFETs throttle down, pulling only the remaining 3A (15A - 12A), keeping them completely cool.
Crucially, 0.2V is still safely above our 0.1V threshold! The regenerative gate remains fully active, the body diodes of your MOSFETs stay reverse-biased due to the positive potential at the D (Drain) junction, and the entire setup works in perfect dynamic equilibrium.
2. Output Stage (Reverse Current Protection)
To secure the power DC-DC converter from blowing up, a dedicated high-current Ideal Diode module (100V, 80A) is installed at its output.
Why it is critical: Without it, a fully charged power station (like an EcoFlow) or a buffer pack would backfeed energy into the converter's output stages once the load is turned off, instantly puncturing its switching transistors. The ideal diode blocks this reverse flow. Thanks to its ultra-low internal resistance, it does not heat up or lower the overall efficiency of the recovery loop.
🛠 Bill of Materials (BOM)
Category	Component	Specification / Details	Qty
Base Load	Atorch Electronic Load	BW600 or equivalent with multi-module slave port support	1 pc
Power Gate	CS120N10 MOSFETs	Genuine N-channel (100V, 120A, TO-220)	2 pcs
Control	LM393 Comparator	Pre-assembled board with potentiometer & 10kΩ pull-up to +12V	1 pc
PCB	Prototyping Perfboard	4x6 cm DIY matrix board	1 pc
Wiring	Power Rails	High-temperature silicone wire (10 AWG, 14 AWG, 16 AWG up to 200°C)	-
DC-DC Converter	Option A (Advanced): Riden	RD6012 / RD6018 laboratory module for total parameter control	1 pc
Option B (Budget): Buck-Boost	ZK-SJ30 module (or equivalent) with external potentiometer knob	1 pc
Protection	Ideal Diode Module	Heavy-duty reverse current protection (100V, 80A)	1 pc
Energy Buffer	Power Station / Battery	EcoFlow, Bluetti (via DC input) or DIY LiFePO4 / Li-ion / Lead-Acid	1 pc
Cooling	Thermalright AXP120-X67	Low-profile CPU heatsinks with copper heatpipes	2 pcs
Ceramic Thermal Pads	Alumina pads (40x40x0.5 mm)	3 pcs
Standard Cooling Fan	120x25 mm fan	1 pc
Slim Cooling Fan	120x15 mm fan	1 pc
Automation	HW-585 PWM Controller	3-channel 12V 5A PC CPU Fan Temperature Control Board (NTC 50K)	1 pc
Hardware	Connectors	XT60 (2 pcs) & High-current XT90 (1 pc)	3 pcs
Enclosure Magnets	Strong 7mm neodymium magnets for screwless 3D-printed cover	-
⚠️ Critical: Slave Board Power Stage Upgrade
Before running any energy recovery tests at power levels above 500W+, it is highly recommended to inspect and upgrade the power transistors on the factory Atorch Slave modules.

The Factory Trap: While the Atorch master unit ("head") usually arrives with decent, genuine Chinese MOSFETs (like the CS50N20), additional factory slave modules often ship with counterfeit, remarked IRFP460 transistors. Under a real heavy load, these fakes experience rapid thermal runaway and fail short.
Author's Solution: In this build, all power MOSFETs (7 pieces total) were unconditionally swapped out for top-tier, authentic IRFP260M keys.
Minimum Required Upgrade: If a full transition to IRFP260M is not viable, you must desolder the fake 460s from your slave boards and replace them at least with genuine, rugged Chinese silicon like the CS50N20. Tests prove this configuration survives stably under continuous load.
