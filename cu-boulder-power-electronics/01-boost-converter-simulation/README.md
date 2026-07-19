<p align="center">
  <img src="./images/fullTransient.png" width="450" alt="Full Output Voltage Transient">
</p>

## 1. What	is	the	steady-state average	output	voltage	(expressed	in	volts)?

### Theoretical Expected Value
If this circuit were 100% perfect and lossless, the math for a boost converter says our $24\text{ V}$ input at a $40\%$ duty cycle ($D = 0.4$) should give us exactly **$40\text{ V}$** on the output:

$$V_{out} = \frac{V_g}{1 - D} = \frac{24\text{ V}}{1 - 0.4} = 40.0\text{ V}$$

### What LTspice Actually Measured
Real circuits have losses, so the simulation won't hit a perfect $40\text{ V}$. After letting the simulation run and settle down ($4.5\text{ ms}$ to $5.0\text{ ms}$), I used `Ctrl + Click` on the `V(vout)` trace to find the true average:

* **Theoretical Perfect Output:** $40.0\text{ V}$
* **Actual Simulated Average :** `40.101 V`
<p align="center">
  <img src="./images/avgVoltage.png" width="450" alt="Full Output Voltage Transient">
</p>

These simulated components are "stealing" that extra voltage:

1. **The Inductor's Wire ($R_L = 100\text{ m}\Omega$):** The inductor has internal copper resistance that burns off power as heat.
2. **The Diode Drop ($D1$):** The `RF1601NS2D` diode takes about $0.8\text{ V}$ to $1\text{ V}$ just to let current pass through it.
3. **The MOSFET Switch ($M1$):** The `IRFS4010` transistor isn't a perfect conductor; it has a tiny internal resistance when turned on.

## 2.	What	is	the	steady-state	average	inductor	current	(in	amps)?

### Theoretical Expected Value
In a boost converter, the inductor is in series with the input source, so its average current must equal the input current. Using power balance:

$$I_{in,avg} = \frac{P_{in}}{V_g} = \frac{P_{out}}{V_g \cdot \eta}$$

From the output power (80.4 W) and knowing losses exist, the input current should be roughly:

$$I_{in,avg} \approx \frac{84.75\text{ W}}{24\text{ V}} \approx 3.53\text{ A}$$

### What LTspice Actually Measured
After the circuit reached steady state (interval: 4.82 ms to 4.99999 ms):

* **Average Inductor Current:** `3.52666 A`
* **RMS Inductor Current:** `3.5384 A`
<p align="center">
  <img src="./images/avgCurrent.png" width="450" alt="Full Output Voltage Transient">
</p>

The average inductor current matches the input current because the inductor sits directly in series with the input source. The boost converter's control circuit modulates the MOSFET to regulate output voltage, but the inductor's average current is determined entirely by how much power the circuit draws from the input. The RMS value is slightly higher due to current ripple — the inductor current oscillates around the average as the MOSFET switches on and off at the switching frequency.

## 3.   What is the steady-state output power (in watts)?
80.4
## 4.   What is the average power drawn out of the input source Vg during steady-state operation of the converter (in watts)?
84.75
## 5.   What is the average power consumption of the gate driver (in watts)?
-204.31mW (incorrect) -->0.20431
## 6.   What is the converter efficiency (enter a numeric value between 0 and 1)?
The formula used:
$$\eta = \frac{80.375\text{ W}}{84.754\text{ W}} \approx \mathbf{0.94833}$$
## 7.   Now change the control voltage input to the pulse-width modulator, so that it produces a control signal having a duty cycle of 0.6. Run the simulation again. What is the new steady-state average output voltage?
180.72 (incorrect)-->60.079