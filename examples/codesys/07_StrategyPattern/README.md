# Project 07: Strategy Pattern (Runtime Algorithm Switch)

## 1. Objective
This project demonstrates the **Strategy Pattern** in IEC 61131-3.
The goal is to change the behavior of a control block (e.g., Temperature Controller) at runtime without modifying its internal code. We switch between an **On/Off Algorithm** (for fast heating) and a **Proportional Algorithm** (for precision) dynamically.

## 2. Technical Implementation
* **Interface (`I_ControlStrategy`):** Defines the common `Calculate` method for all algorithms.
* **Strategies:**
    * `FB_Strategy_OnOff`: Returns 100% or 0% based on a threshold.
    * `FB_Strategy_Proportional`: Calculates output based on error and gain (Kp).
* **Context (`FB_TempController`):** It holds a reference to the active strategy. It delegates the math to the selected strategy using the interface.

## 3. How to Test in Simulation
Follow these steps to see the algorithm switch in action.

### Step 1: Default Mode (On/Off)
1.  Login and run the project. By default, **On/Off** strategy is active.
2.  Set `rSetPoint` to **50.0** and `rActualTemp` to **45.0**.
    * **Result:** `rSystemOutput` is **100.0** (Full Power because 45 < 48).
3.  Set `rActualTemp` to **51.0**.
    * **Result:** `rSystemOutput` is **0.0**.
    * *Observation:* The output jumps between 0 and 100.

### Step 2: Switch to Proportional Mode
1.  Set `bSelectProp` to **TRUE**. The controller now uses the P-Control logic.
2.  Set `rSetPoint` to **50.0** and `rActualTemp` to **45.0**.
    * **Math:** Error = 5.0, Gain (Kp) = 5.0. Output = 25.0.
    * **Result:** `rSystemOutput` is **25.0**.
3.  Set `rActualTemp` to **40.0**.
    * **Math:** Error = 10.0. Output = 50.0.
    * **Result:** `rSystemOutput` is **50.0**.
    * *Observation:* The output changes smoothly based on the error.

### Step 3: Switch Back
1.  Set `bSelectOnOff` to **TRUE**.
2.  Observe that `rSystemOutput` immediately jumps back to **100.0** (for the same 40.0 degree input), proving the logic has completely changed.

## 4. Key Variables to Monitor
| Variable | Location | Description |
| :--- | :--- | :--- |
| `rSystemOutput` | PLC_PRG | The final control signal (0-100%). |
| `_iActiveStrategy` | FB_TempController | Shows which strategy FB is currently linked. |
| `bSelectProp` | PLC_PRG | Trigger to switch to Proportional control. |
| `bSelectOnOff` | PLC_PRG | Trigger to switch to On/Off control. |