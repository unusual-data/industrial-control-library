# Project 11: Recipe Manager (File I/O & Serialization)

## 1. Objective
This project demonstrates **Data Persistence** in IEC 61131-3.
It enables the PLC to save structural data (`ST_Recipe`) to the file system as a **CSV file** and read it back. This is essential for managing machine parameters and exchanging data with external systems.

## 2. Technical Implementation
* **Serialization:** Converts a Struct (`Name,Speed,Temp`) into a CSV string using `CONCAT`.
* **Platform Independence:** Uses `__XWORD` data types for file handles/sizes to ensure compatibility with both **32-bit PLCs** and **64-bit Simulations**.
* **Library Management:** Uses explicit namespace (`SysFile.AM_WRITE`) to avoid conflicts with other libraries.
* **Sandbox Compliance:** Uses relative file paths to adhere to Windows/CODESYS security permissions.

## 3. How to Test (Simulation)

### Step 1: Configuration
1.  Open `PLC_PRG`.
2.  Ensure the file path is set to a **relative path**:
    ```iec
    sFilePath : STRING(255) := 'MyRecipe.csv';
    ```
    *Note: Do not use absolute paths like `C:\` to avoid "Access Denied" (Error 39).*

### Step 2: Save Operation
1.  Login to CODESYS (Simulation Mode).
2.  Set `bCmdSave` to **TRUE**.
3.  Verify that `bDone` becomes TRUE and `bError` remains FALSE.

### Step 3: Locating the File (Critical)
In Simulation mode, CODESYS writes to its isolated working directory inside `AppData`.

1.  Open Windows File Explorer.
2.  Paste the following path into the address bar and press Enter:
    `%APPDATA%\CODESYS\Simulation\PlcLogic`
3.  You will find **`MyRecipe.csv`** in this folder.
4.  Open it with Notepad to verify the content: `Gold_Setup,1450.5,85.0`

### Step 4: Load Operation
1.  Modify the CSV file in Notepad (e.g., change values to `NewMode,500,20`) and save it.
2.  In CODESYS, set `bCmdLoad` to **TRUE**.
3.  Monitor `stLoadedRecipe`. It should update with the new values from the file.

## 4. Key Variables
| Variable | Description |
| :--- | :--- |
| `stCurrentRecipe` | Data structure in PLC memory to be saved. |
| `stLoadedRecipe` | Data structure populated from the file. |
| `dwErrorID` | System error code (0 = Success, 39 = Access Denied). |