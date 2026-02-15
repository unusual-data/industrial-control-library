# Project 02: Generic Motor Drive (Inheritance)

## 1. Objective
This project demonstrates **Inheritance** in IEC 61131-3. We developed a hierarchical drive architecture where specialized drives (VFD and Servo) inherit core functionalities from a base class (`FB_DriveBase`).

## 2. What we did in this project
* **Base Class Pattern:** Created `FB_DriveBase` to handle universal logic like Enable, Reset, and the main State Machine.
* **Inheritance:** Used the `EXTENDS` keyword to derive `FB_VFD` and `FB_Servo`.
* **Code Reusability:** The core logic is written once in the base class and called by derived classes using `SUPER^()`.
* **Scalability:** The structure makes it easy to add new drive types without rewriting basic logic.

## 3. How to Test in Simulation
1. Set `bSystemStart` to **TRUE**.
2. For `fbConveyorVFD`: Set `lrSetSpeed` to 750. Observe `FrequencyOutput` calculating 25Hz (50% of Max).
3. Monitor `fbArmServo`. Check `Active` status and you will see `ActualPosition` moving towards the target (0 -> 180) automatically.
4. Set `bSystemStart` to **FALSE** and observe how the base state machine handles the shutdown for both drives simultaneously.

## 4. Key Variables to Monitor
| Variable | Origin | Description |
| :--- | :--- | :--- |
| `_eState` | Base | Shared state machine for all drives. |
| `FrequencyOutput` | VFD | Specific output, only available in VFD class. |
| `InPosition` | Servo | Specific status, only available in Servo class. |
| `ActualPosition` | Servo | Simulated position feedback value. |




