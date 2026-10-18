# Two-Stage CMOS Miller Operational Amplifier (180nm CMOS)

An analog integrated circuit design and simulation of a high-gain, wideband **Two-Stage CMOS Miller Operational Amplifier** designed for a **1.8V 180nm CMOS** process.

This project was developed as part of an **Analog IC Design Portfolio** targeting the **Analog Engineer** position at **Texas Instruments**.

---

## 📊 Key Performance Metrics

| Performance Metric | Design Target | Achieved (LTspice) | Status |
| :--- | :--- | :--- | :--- |
| **Technology Node** | 180nm CMOS | 180nm Level 1 BSIM | Verified |
| **Supply Voltage ($V_{DD}$)** | $1.8\text{ V}$ | $1.8\text{ V}$ | Verified |
| **Load Capacitance ($C_L$)** | $2.0\text{ pF}$ | $2.0\text{ pF}$ | Verified |
| **DC Open-Loop Gain ($A_v$)** | $\ge 60\text{ dB}$ | **$83.89\text{ dB}$ ($\approx 15,650\times$)** | **Exceeded** |
| **Unity Gain Bandwidth ($UGBW$)** | $\ge 15\text{ MHz}$ | **$16.67\text{ MHz}$** | **Passed** |
| **Phase Margin ($PM$)** | $\ge 60^\circ$ | **$75.87^\circ$** | **Exceeded** |
| **Slew Rate ($SR$, rising)** | $\ge 10\text{ V}/\mu\text{s}$ | **$18.2\text{ V}/\mu\text{s}$** | **Passed** |
| **Quiescent Current ($I_q$)** | $< 150\ \mu\text{A}$ | **$120\ \mu\text{A}$** | **Passed** |

---

## 🏛️ Circuit Architecture

```
               VDD (1.8V)
             +-----------+-----------------------+
             |           |                       |
          +--+--+     +--+--+                 +--+--+
          |  M3 |     |  M4 |                 |  M6 |
       +--|     |--+--|     |                 |     | (PMOS CS Driver)
       |  +--+--+  |  +--+--+                 +--+--+
       |     |     |     |                       |
       +-----+-----+     | Node 1                |
             |           |                       |
             |        +--+------[ Rz ]----||-----+-------> Vout
             |        |                   Cc     |
          +--+--+     |  +--+--+              +--+--+
  Vin- >--|  M1 |     +--|  M2 |--< Vin+      |  M7 |
          |     |        |     |              |     | (NMOS Load)
          +--+--+        +--+--+              +--+--+
             |              |                    |
             +-------+------+                    |
                     |                           |
                  +--+--+                        |
                  |  M5 | (Tail Current)         |
                  |     |                        |
                  +--+--+                        |
                     |                           |
             +-------+---------------------------+
             |
            GND (0V)
```

### Transistor Sizing Details ($L = 0.5\ \mu\text{m}$ for high $r_o$):
* **Input Pair ($M_1, M_2$):** $W = 2.0\ \mu\text{m}, L = 0.5\ \mu\text{m}, I_D = 10\ \mu\text{A}$ each
* **Current Mirror Load ($M_3, M_4$):** $W = 5.0\ \mu\text{m}, L = 0.5\ \mu\text{m}, I_D = 10\ \mu\text{A}$ each
* **Tail Current Source ($M_5$):** $W = 3.0\ \mu\text{m}, L = 0.5\ \mu\text{m}, I_{tail} = 20\ \mu\text{A}$
* **Second Stage Driver ($M_6$):** $W = 40.0\ \mu\text{m}, L = 0.5\ \mu\text{m}, I_D = 80\ \mu\text{A}$
* **Second Stage Sink ($M_7$):** $W = 12.0\ \mu\text{m}, L = 0.5\ \mu\text{m}, I_D = 80\ \mu\text{A}$
* **Compensation:** $C_c = 1.0\text{ pF}$, Nulling Resistor $R_z = 1.2\text{ k}\Omega$

---

## 📈 Simulation Results

### 1. Small-Signal AC Frequency Response (Bode Plot)
* **Open-Loop Gain:** $83.89\text{ dB}$
* **$-3\text{ dB}$ Corner Frequency:** $\approx 1\text{ kHz}$
* **Unity Gain Crossover:** $16.67\text{ MHz}$ at a phase of $-104.13^\circ$, yielding a **$75.87^\circ$ Phase Margin**.

*(Add screenshot: `images/bode_plot.png`)*

### 2. Large-Signal Step Response (Slew Rate Verification)
* **Configuration:** Closed-loop unity-gain buffer ($V_{out}$ fed back to $V_{in-}$).
* **Input Stimulus:** $0.8\text{ V}$ step ($0.5\text{ V} \to 1.3\text{ V}$) with $1\text{ ns}$ edge.
* **Measured Slew Rate:** **$18.2\text{ V}/\mu\text{s}$** (analytical hand calculation predicted $\frac{I_{tail}}{C_c} = \frac{20\ \mu\text{A}}{1.0\text{ pF}} = 20\text{ V}/\mu\text{s}$, giving a **$91\%$ theoretical correlation**).
* **Stability:** Zero overshoot and zero ringing during step settling.

*(Add screenshot: `images/transient_response.png`)*

---

## 🛠️ Design Insights & Trade-offs

1. **Pole Splitting via Miller Effect:**
   Using capacitor $C_c$ across the inverting second stage moves the dominant pole to $\omega_{p1} \approx \frac{1}{R_{out1} g_{m6} R_{out2} C_c}$ and pushes the output pole to $\omega_{p2} \approx \frac{g_{m6}}{C_L}$. This separates the two poles and stabilizes the feedback loop.
2. **Right-Half-Plane (RHP) Zero Nulling:**
   The feedforward path through $C_c$ introduces an RHP zero at $\omega_z = +\frac{g_{m6}}{C_c}$, which degrades phase margin. Adding a series resistor $R_z = \frac{1}{g_{m6}} \approx 1.2\text{ k}\Omega$ pushes this zero to infinity.
3. **Systematic Offset Elimination:**
   Sized to satisfy $\frac{(W/L)_6}{(W/L)_3} = 2 \times \frac{(W/L)_7}{(W/L)_5} = \frac{I_6}{I_1} = 8$, forcing $V_{DS3} = V_{DS4}$ under quiescent balanced conditions to eliminate systematic input offset voltage.

---

## 📂 Repository File Structure

```
├── README.md                     # Project documentation & design summary
being committed
├── two_stage_opamp_ac.cir        # LTspice AC analysis testbench (Gain, UGBW, Phase Margin)
├── two_stage_opamp_transient.cir # LTspice Transient analysis testbench (Slew Rate)
└── images/                       # Waveform screenshots
    ├── bode_plot.png
    └── transient_response.png
```

---

## 🚀 How to Run the Simulations

1. Clone this repository:
   ```bash
   git clone https://github.com/<your-username>/two-stage-cmos-opamp.git
   ```
2. Open **LTspice**.
3. To view frequency response: Open `two_stage_opamp_ac.cir` and click **Run (🏃‍♂️)**. Add trace `V(vout)`.
4. To view transient step response: Open `two_stage_opamp_transient.cir` and click **Run (🏃‍♂️)**. Add traces `V(vin_plus)` and `V(vout)`.
