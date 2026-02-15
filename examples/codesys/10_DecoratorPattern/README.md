# Project 10: Decorator Pattern (Dynamic Feature Wrapper)

## 1. Objective
This project demonstrates the **Decorator Pattern** in IEC 61131-3.
The goal is to add features (like Safety Checks and Logging) to a standard object dynamically without modifying its code or creating complex inheritance trees. We achieve this by "wrapping" the object in layers of decorators.

## 2. Technical Implementation
* **Core (`FB_StandardCylinder`):** The basic actuator that controls the physical valves.
* **Decorator Base (`FB_ActuatorDecorator`):** A wrapper that implements `I_Actuator` and holds a reference to another actuator. It delegates all calls to the wrapped object.
* **Concrete Decorators:**
    * `FB_WithSafety`: Checks a boolean permission (`bSafetyOk`) before passing the command down.
    * `FB_WithLogging`: Writes a log message to a string variable before/after execution.
* **Composition:** In `PLC_PRG`, we chain them: `Logger` wraps `Safety`, which wraps `Cylinder`.

## 3. How to Test in Simulation
Follow these steps to see how the layers interact.

### Step 1: Initialize System
1.  Login and run the project.
2.  The `bInit` logic constructs the chain: **Logger -> Safety -> Core**.

### Step 2: Test Blocked Attempt (Safety Active)
1.  Ensure `bSafetySwitch` is **FALSE**.
2.  Set `bCmdWork` to **TRUE**.
3.  **Observe `fbLoggerLayer`:** `sLastAction` shows "LOG: Move Work Command Sent.". (The Logger did its job).
4.  **Observe `fbCoreCylinder`:** `bValveWork` remains **FALSE**.
    * *Reason:* The `fbSafetyLayer` received the call from Logger but blocked it because safety was off.

### Step 3: Test Successful Move
1.  Set `bSafetySwitch` to **TRUE**.
2.  Set `bCmdWork` to **TRUE**.
3.  **Observe `fbCoreCylinder`:** `bValveWork` becomes **TRUE**.
    * *Reason:* Logger -> (Log) -> Safety -> (Check OK) -> Core -> (Valve Open).

### Step 4: Verify "Home" Command
1.  Set `bCmdHome` to **TRUE**.
2.  Observe `fbLoggerLayer` logs "Move Home".
3.  Observe `fbCoreCylinder` switches to `bValveHome`.

## 4. Key Variables to Monitor
| Variable | Location | Description |
| :--- | :--- | :--- |
| `sLastAction` | FB_WithLogging | Shows the log output from the outer layer. |
| `bSafetySwitch` | PLC_PRG | Controls the permission in the middle layer. |
| `bValveWork` | FB_StandardCylinder | The physical output of the core layer. |