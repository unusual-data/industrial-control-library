# Industrial Software Engineering - Naming & Coding Standard

**Standard:** PLCopen Guidelines (Extended) & Clean Code  
**Platform:** IEC 61131-3 (TwinCAT 3 / CODESYS V3) & IEC 61499 Concepts  
**Objective:** Zero-Ambiguity, IT/OT Convergence, High Maintainability

---

## 1. Object Naming (Type Definitions)
Identify object types immediately from the project tree.

| Object Type | Prefix | Example | Description |
| :--- | :--- | :--- | :--- |
| **Program** | `PRG_` | `PRG_Production` | Main execution entry point. |
| **Function Block** | `FB_` | `FB_ServoDrive` | Object with internal memory. |
| **Function** | `F_` | `F_CalcCRC32` | Stateless logic/math. |
| **Interface** | `I_` | `I_Observable` | Contract definition (No code). |
| **Structure** | `ST_` | `ST_MotionParams` | Data structure definition. |
| **Enumeration** | `E_` | `E_State` | Finite state list definition. |
| **Union** | `U_` | `U_DataMap` | Shared memory definition. |
| **Global Var** | `GVL_` | `GVL_Config` | Global variable list. |

---

## 2. Variable Naming (Strict Hungarian)
Variable names must indicate **Scope** and **Data Type**.

### A. Scope Prefixes (Visibility)
Defines where the variable lives and who can touch it.

| Scope | Prefix | Example | Rule |
| :--- | :--- | :--- | :--- |
| **VAR_INPUT** | **None** | `Execute`, `Velocity` | **External Interface:** Keep clean. Acts like a Property. |
| **VAR_OUTPUT** | **None** | `Done`, `ErrorID` | **External Interface:** Keep clean. Acts like a Property. |
| **VAR_IN_OUT** | **None** | `AxisRef`, `Buffer` | **Reference:** Bi-directional data transfer. |
| **VAR** (Internal) | `_` | `_nState`, `_bBusy` | **Private:** Internal logic only. Must start with underscore. |
| **VAR_STAT** | `st_` | `st_nCount` | **Static:** Persists between cycles. |
| **CONSTANT** | `c_` | `c_MAX_LIMIT` | **Constant:** Upper case, fixed values. |

### B. Data Type Prefixes (Efficiency)
Use these prefixes for **Internal Variables (`VAR`)** and **Method Arguments**.

#### 1. Elementary Types
| Type | Prefix | Example | Notes |
| :--- | :--- | :--- | :--- |
| **BOOL** | `b` | `bEnabled` | Logical True/False. |
| **INT Types** | `n` | `nCounter` | INT, DINT, LINT (Math/Logic). |
| **UINT Types** | `u` | `uID` | UINT, UDINT (IDs, Counts). |
| **REAL** | `r` | `rTemp` | 32-bit Float. |
| **LREAL** | `lr` | `lrPosition` | 64-bit Float (Precision). |
| **STRING** | `s` | `sName` | ASCII Text. |
| **TIME** | `t` | `tDuration` | Time/Timer values. |

#### 2. Complex & Reference Types
Distinguish custom FBs from standard IEC primitives (Timers/Triggers) to prevent ambiguity.

| Type | Prefix | Example | Notes |
| :--- | :--- | :--- | :--- |
| **FB Instance** | `fb` | `fbServo` | Custom Function Block instance (`FB_`). |
| **TON** | `ton` | `tonStartDelay` | Timer On-Delay (IEC Standard). |
| **TOF** | `tof` | `tofStopDelay` | Timer Off-Delay (IEC Standard). |
| **R_TRIG** | `rt` | `rtExecute` | Rising Edge Trigger. |
| **F_TRIG** | `ft` | `ftCapture` | Falling Edge Trigger. |
| **STRUCT Instance** | `st` | `stParams` | Instance of a Structure (`ST_`). |
| **ENUM Instance** | `e` | `eState` | Instance of an Enum (`E_`). |
| **INTERFACE** | `ip` | `ipLogger` | Interface Pointer (Critical for OOP). |
| **REFERENCE** | `ref` | `refConfig` | Managed memory reference. |
| **POINTER** | `p` | `pBuffer` | Raw memory address (Avoid if possible). |
| **ARRAY** | `a` | `aData` | Collection of data. |


---

## 3. Implementation Style

### A. The "Hybrid" Approach
* **External:** Clean, readable names for the user.
* **Internal:** Strict typing for the developer (you).

```iec
FUNCTION_BLOCK FB_SmartConveyor IMPLEMENTS I_Conveyor
VAR_INPUT
    Enable      : BOOL;         // External: Clean
    Velocity    : LREAL;        // External: Clean
    Config      : ST_Config;    // External: Clean
END_VAR
VAR_OUTPUT
    Busy        : BOOL;         
    Error       : BOOL;
END_VAR
VAR
    // Internal: Strict Hungarian + Underscore
    _bInitialized : BOOL;       // Flag
    _nState       : INT;        // State Machine
    _tonWatchdog  : TON;        // Timer Primitive(Specific)
    _rtStartCmd   : R_TRIG;     // Trigger Primitive(Specific)
    _fbMotion     : FB_Motion;  // Custom FB Instance(General)
    _ipLogger     : I_Logger;   // Interface Dependency
    _stParams     : ST_Config;  // Struct Instance
END_VAR
```

### B. Enum Definition & Naming
Enums must use the `{attribute 'qualified_only'}` pragma to enforce namespace encapsulation.
* **Type Name:** Starts with `E_`
* **Members:** Pure **PascalCase** (No prefix).
* **Access:** Must be fully qualified (e.g., `E_State.Idle`).

```iec
{attribute 'qualified_only'}
{attribute 'strict'}
TYPE E_MachineState :
(
    Idle       := 0,
    Running    := 1,
    Error      := 99
) UINT;
END_TYPE

// Usage Example:
// _eState := E_MachineState.Idle;
```

---

## 4. Repository Structure
Follow this structure to support IT/OT modularity.

```text
industrial-control-library/
├── docs/               # Architecture & Standards
├── src/
│   ├── definitions/    # Shared Types (DUTs)
│   │   ├── interfaces/ # I_ (Contracts)
│   │   ├── structs/    # ST_ (Data)
│   │   └── enums/      # E_  (States)
│   ├── components/     # Hardware Logic (FB_)
│   ├── patterns/       # Design Patterns (FB_ w/o Hardware)
│   ├── gateways/       # Communication (MQTT, OPC UA)
│   └── distributed/    # IEC 61499 / Event-Driven Blocks
├── tests/              # Unit Tests
└── examples/           # Usage Demos
```

---

## 5. Critical Rules
1.  **External Interface:** NO Prefixes in `VAR_INPUT` / `VAR_OUTPUT`.
2.  **Internal State:** ALWAYS use Underscore `_` + Prefix for `VAR`.
3.  **Dependencies:** Use `INTERFACE` (`ip`) instead of concrete FBs where possible.
4.  **No Magic Numbers:** Use `CONSTANT` for fixed values.
5.  **Primitives:** Use specific prefixes (ton, tof, rt) for IEC standard blocks, not generic fb.