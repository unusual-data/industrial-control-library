# Project 08: State Pattern (OOP State Machine)

## 1. Objective
This project demonstrates the **State Pattern** in IEC 61131-3.
In complex machines (like PackML), using a giant `CASE` statement creates hard-to-maintain "spaghetti code". Here, we treat every State as a separate Object (Class). The Machine (Context) simply delegates the work to the **Active State**.

## 2. Technical Implementation
* **Context (`FB_Machine`):** Owns the data (Motor, Lights) and the instances of the states. It has a `_iCurrentState` variable holding the active logic.
* **Interface (`I_State`):** Defines the `Handle(pMachine)` method.
* **Pointer (`POINTER TO FB_Machine`):** Used in the `Handle` method to allow the State object to modify the Machine's variables (Outputs, State Transitions) without creating circular dependency errors.
* **States:**
    * `FB_State_Idle`: Sets Yellow Light. Waits for Start command.
    * `FB_State_Starting`: Sets Green Light. Waits 2 seconds (Timer).
    * `FB_State_Execute`: Turns Motor ON. Waits for Stop command.

## 3. How to Test in Simulation
Follow these steps to observe the state transitions.

### Step 1: Default State (Idle)
1.  Login and run the project.
2.  Observe `fbPackMLUnit`.
    * `_iCurrentState` points to `FB_State_Idle`.
    * `bLightYellow` is **TRUE**.
    * `bMotor` is **FALSE**.

### Step 2: Transition to Starting -> Execute
1.  Set `bButtonStart` to **TRUE**.
2.  **Immediate Effect:** The machine switches to `FB_State_Starting`.
    * `bLightYellow` turns OFF.
    * `bLightGreen` turns ON.
3.  **After 2 Seconds:** The machine automatically switches to `FB_State_Execute`.
    * `bMotor` becomes **TRUE**.
    * The machine is now in production mode.

### Step 3: Stop the Machine
1.  Set `bButtonStop` to **TRUE**.
2.  Observe that `_iCurrentState` switches back to `FB_State_Idle`.
3.  `bMotor` stops and `bLightYellow` turns ON again.

## 4. Key Variables to Monitor
| Variable | Location | Description |
| :--- | :--- | :--- |
| `_iCurrentState` | FB_Machine | Interface pointing to the active logic block. |
| `bMotor` | FB_Machine | Physical output controlled by the Execute state. |
| `pMachine` | I_State.Handle | Pointer providing access to the machine context. |