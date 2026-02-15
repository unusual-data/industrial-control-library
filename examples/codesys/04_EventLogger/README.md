# Project 04: Centralized Event Logger (Singleton Pattern)

## 1. Objective
This project implements a **Centralized Event Logger** using the **Singleton Pattern**.
In industrial machines, tracking errors and status changes is critical. Instead of passing a logger object to every function block, we create a single global instance (`GVL_App.Logger`) that can be accessed from anywhere in the code.

## 2. Technical Implementation
* **Singleton Pattern:** The `FB_Logger` is instantiated once in a Global Variable List (`GVL_App`). This provides global access without dependency injection.
* **Ring Buffer (Circular Buffer):** The logger uses a fixed array of 50 elements. When the buffer is full, it automatically overwrites the oldest entry. This prevents memory overflow.
* **Structured Data:** Logs are stored as `ST_LogEntry`, containing an ID, Timestamp, Level (Info/Error), and Message.
* **Encapsulation:** The internal buffer (`_aLogBuffer`) is hidden. External code interacts only via the `.AddLog()` method.

## 3. How to Test in Simulation
Follow these steps to verify the logging logic in CODESYS:

### Step 1: Initialization
1.  Login and run the project.
2.  Open **GVL_App** in the watch window to monitor `Logger._aLogBuffer`.

### Step 2: Generate Logs
1.  In `PLC_PRG`, set `bLogInfo` to **TRUE**.
    * **Result:** A new entry appears in the buffer with `eLevel = Info`.
2.  Set `bLogError` to **TRUE**.
    * **Result:** A new entry appears with `eLevel = Error` and a unique `nID`.

### Step 3: Test Ring Buffer
1.  Trigger log events more than 50 times.
2.  Observe that the `_nWriteIndex` resets to 1 and starts overwriting the old logs (Index 1, 2, 3...).
3.  The `nID` counter continues to increase (51, 52, 53...), ensuring traceabilty even if old data is overwritten.

## 4. Key Variables to Monitor
| Variable | Location | Description |
| :--- | :--- | :--- |
| `GVL_App.Logger` | Global | The main logger instance. |
| `_aLogBuffer` | FB_Logger | The array holding the log history. |
| `_nWriteIndex` | FB_Logger | The pointer showing where the next log will be written. |
| `_nNextID` | FB_Logger | The unique auto-increment ID counter. |