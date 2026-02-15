# Project 03: Polymorphic Sensor System (Interfaces)

## 1. Objective
This project demonstrates the power of **Polymorphism** and **Interfaces** in Industrial Software Engineering.
Instead of writing separate logic for Digital and Analog sensors, we define a common contract (`I_Sensor`). This allows the main program to treat all sensors exactly the same, reducing code duplication and making the system easy to expand.

## 2. Technical Implementation
* **Interface (`I_Sensor`):** A standard contract that forces every sensor to have a `.Read()` method and a `.Status` property.
* **Concrete Classes:**
    * `FB_DigitalSensor`: Converts Boolean signals (TRUE/FALSE) to normalized Real values (1.0/0.0).
    * `FB_AnalogSensor`: Handles raw integer scaling (0-32767) to engineering units (e.g., Bar, Celsius).
* **Polymorphism:** In `PLC_PRG`, we store different sensor types in a single array (`ARRAY OF I_Sensor`) and process them in a single loop.

## 3. How to Test in Simulation
Follow these steps to verify the logic in the CODESYS simulation environment:

### Step 1: System Startup
1.  Login and run the project.
2.  Check the `aSystemSensors` array in the watch list. Ensure the elements are not "0" (Null).

### Step 2: Testing the Digital Sensor (Index 1)
1.  Set `bSimDigitalIn` to **TRUE**.
2.  Observe `rMonitorValues[1]`. It should become **1.0**.
3.  Set `bSimDigitalIn` to **FALSE**. It should become **0.0**.

### Step 3: Testing the Pressure Sensor (Index 2)
1.  The sensor is configured for **0 to 10 Bar**.
2.  Set `nSimPressureIn` to **16384** (approx. 50% of the raw input range).
3.  Observe `rMonitorValues[2]`. It should be approximately **5.0**.

### Step 4: Testing the Temperature Sensor (Index 3)
1.  The sensor is configured for **-50 to 150 Celsius**.
2.  Set `nSimTempIn` to **0**.
3.  Observe `rMonitorValues[3]`. It should be **-50.0**.

## 4. Key Variables to Monitor
| Variable | Type | Description |
| :--- | :--- | :--- |
| `aSystemSensors` | ARRAY OF I_Sensor | The container holding different sensor objects. |
| `rMonitorValues` | ARRAY OF REAL | The final output values read from the sensors. |
| `bSimDigitalIn` | BOOL | Simulation input for the Digital Sensor. |
| `nSimPressureIn` | INT | Simulation input for the Pressure Sensor. |
| `nSimTempIn` | INT | Simulation input for the Temperature Sensor. |