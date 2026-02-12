# Project 01: Smart Cylinder Control (OOP Basics)

## 1. Objective
The goal of this project is to demonstrate **Encapsulation** and **State Machine** design in industrial automation. This project applies **Object-Oriented Programming (OOP)** principles to the IEC 61131-3 standard, following the repository's coding guidelines to ensure maintainability and IT/OT alignment.

## 2. Technical Implementation
* **Encapsulation:** All complex logic (timing, valve control, and error handling) is contained inside the Function Block (`FB_PneumaticCylinder`).
* **Method-Based Control:** Instead of manual bit manipulation, the cylinder is controlled via clear public methods: `.MoveWork()` and `.MoveHome()`.
* **Naming Standards:** The code strictly follows the **Strict Hungarian Notation** and **Scope Prefixes** (e.g., `_` for internal variables) defined in the project standards.
* **Safety:** A built-in **Watchdog Timer** (`_tonWatchdog`) monitors motion to detect hardware failures or jams.

## 3. How to Test in Simulation

Follow these steps to verify the logic in the CODESYS simulation environment:

### Step 1: System Startup
1.  Set `bStartSystem` to **TRUE**.
2.  Verify that `bReady` is **TRUE** and `nStateID` is **0** (Idle).

### Step 2: Normal Operation (Success Path)
1.  Set `bGoWork` to **TRUE** (and then back to **FALSE**).
2.  Observe: `bBusy` becomes **TRUE**, `bValveWork` becomes **TRUE**, and `nStateID` changes to **10**.
3.  Manually set `bSensorWork` to **TRUE** within the timeout period (10s).
4.  Observe: `bBusy` becomes **FALSE** and `nStateID` changes to **20** (AtWork).

### Step 3: Error Handling (Timeout Path)
1.  Set `bGoHome` to **TRUE**.
2.  **Do not** trigger any sensors. Wait for 10 seconds.
3.  Observe: `bError` becomes **TRUE**, all valve outputs shut down, and `nStateID` changes to **99**.
4.  To recover, set `bReset` to **TRUE**.

## 4. Key Variables to Monitor

| Variable | Type | Description |
| :--- | :--- | :--- |
| `bStartSystem` | BOOL | Main enable for the control block. |
| `bGoWork / bGoHome` | BOOL | Command triggers for cylinder movement. |
| `bSensorWork / bSensorHome`| BOOL | Simulated sensor feedback from the field. |
| `bReady` | BOOL | Indicates the system is healthy and waiting. |
| `bError` | BOOL | Indicates a motion timeout or system failure. |
| `nStateID` | UINT | Current step of the internal state machine. |