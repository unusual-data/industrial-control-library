# Project 05: Command Pattern (FIFO Queue System)

## 1. Objective
This project demonstrates the **Command Pattern** using a **FIFO (First-In, First-Out) Queue**.
In asynchronous systems (like Robot Control or CNC), the HMI sends commands faster than the machine can execute them. We use a buffer to store these commands and process them one by one.

## 2. Technical Implementation
* **FIFO Queue (`FB_CommandQueue`):** A Ring Buffer that stores up to 10 commands. It uses `Head` and `Tail` pointers to manage data flow efficiently without shifting the array.
* **Encapsulation:** The internal array `_aBuffer` is private. Commands can only be added via `.Enqueue()` and removed via `.Dequeue()` methods.
* **Producer-Consumer Model:**
    * **Producer (HMI):** Adds commands to the queue (e.g., "Move to 150", "Grip").
    * **Consumer (Robot):** Automatically pulls the next command from the queue only when it is ready (`bRobotReady`).

## 3. How to Test in Simulation
The PLC cycle is faster than human reaction. To see the queue filling up, you must **pause the consumer (Robot)** first.

### Step 1: Initialize System
1.  Login and run the project.
2.  **CRITICAL STEP:** Manually set `bRobotReady` to **FALSE** in the watch list.
    * This forces the robot to be "Busy".
    * The consumer logic stops pulling data from the queue.

### Step 2: Add Commands (Producer)
1.  Set `bAddMove` to **TRUE** (then back to FALSE).
    * Observe: `fbQueue.Count` becomes **1**.
2.  Set `bAddGrip` to **TRUE** (then back to FALSE).
    * Observe: `fbQueue.Count` becomes **2**.
3.  Repeat this to add multiple commands. You will see them stacking up in the `_aBuffer`.

### Step 3: Process Commands (Consumer)
1.  Set `bRobotReady` to **TRUE**.
    * Observe: The robot immediately takes the first command (`MoveAbs`).
    * `fbQueue.Count` decreases by 1.
2.  Toggle `bRobotReady` (**FALSE** then **TRUE**) to simulate the robot finishing the task.
    * Observe: The robot takes the next command (`Grip`).
    * `fbQueue.Count` continues to decrease until it reaches 0.

## 4. Key Variables to Monitor
| Variable | Location | Description |
| :--- | :--- | :--- |
| `fbQueue.Count` | PLC_PRG | Number of waiting commands. |
| `stActiveCmd` | PLC_PRG | The command currently being executed by the robot. |
| `bRobotReady` | PLC_PRG | Flag indicating the robot is free to take new work. |
| `_nHead / _nTail` | FB_CommandQueue | Internal pointers for ring buffer logic. |
| `_aBuffer` | FB_CommandQueue | Raw data array. **Note:** Old data remains visible even after processing until overwritten. |