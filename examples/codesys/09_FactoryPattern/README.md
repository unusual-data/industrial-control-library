# Project 09: Factory Pattern (Dynamic Object Creation)

## 1. Objective
This project demonstrates the **Factory Pattern** in IEC 61131-3.
The goal is to decouple the main control logic from specific hardware implementations. The main program does not instantiate specific drives (e.g., Siemens or Beckhoff). Instead, it asks a **Factory Block** to provide a generic `I_Drive` interface based on the configuration.

## 2. Technical Implementation
* **Interface (`I_Drive`):** Defines the standard methods (`Start`, `Stop`, `SetSpeed`) that all drives must implement.
* **Concrete Products:**
    * `FB_Drive_BrandA`: Implements `I_Drive` with logic specific to Brand A (e.g., uses REAL speed).
    * `FB_Drive_BrandB`: Implements `I_Drive` with logic specific to Brand B (e.g., converts speed to INT).
* **Factory (`FB_DriveFactory`):** Contains instances of all supported drives. Based on the `eSelectedBrand` input, it assigns the correct instance to the output interface (`iDrive`).
* **Static Allocation:** Unlike C# or Java, we pre-allocate memory for all drives inside the Factory to ensure stability and deterministic behavior in the PLC.

## 3. How to Test in Simulation
Follow these steps to verify the factory logic.

### Step 1: Default Configuration (Brand A)
1.  Login and run the project. `eConfigBrand` is `Brand_A` by default.
2.  Set `bStartCmd` to **TRUE**.
3.  Expand `fbFactory._fbDriveA`. Observe that `_bActive` becomes **TRUE** and `_rSpeed` is **1500.0**.
4.  Expand `fbFactory._fbDriveB`. Observe that it remains inactive.

### Step 2: Change Configuration (Brand B)
1.  **Logout** from simulation.
2.  Change `eConfigBrand` to `Brand_B` in `PLC_PRG`.
3.  **Login with Online Change** (or Download).
4.  Set `bInit` to **TRUE** (to force factory update).
5.  Set `bStartCmd` to **TRUE**.
6.  Expand `fbFactory._fbDriveB`. Observe that `_bRunning` becomes **TRUE** and `_nVelocity` is **1500** (INT).
7.  The main program logic did not change, but the underlying hardware behavior did.

## 4. Key Variables to Monitor
| Variable | Location | Description |
| :--- | :--- | :--- |
| `iMainMotor` | PLC_PRG | The generic interface controlling the active drive. |
| `eConfigBrand` | PLC_PRG | Setting that determines which hardware to use. |
| `_fbDriveA` | FB_DriveFactory | The concrete instance for Brand A. |
| `_fbDriveB` | FB_DriveFactory | The concrete instance for Brand B. |